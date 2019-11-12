<template>
  <v-container class="mx-auto">
    <v-form>
      <v-card class="ma-5 pa-5 secondary">
        <v-card-title>
          Enter basic beneficiary information and video recipient contact
          information.
        </v-card-title>
        <v-row>
          <v-text-field
            type="text"
            class="mx-2"
            v-model="gratitudeMessage.beneficiaryName"
            label="Beneficiary Name"
            outlined
            rounded
          />
          <v-text-field
            type="text"
            class="mx-2"
            v-model="gratitudeMessage.recipientName"
            label="Recipient Name"
            outlined
            rounded
          />
          <v-text-field
            type="email"
            label="Email"
            class="mx-2"
            outlined
            rounded
            v-model="gratitudeMessage.recipientEmail"
          />
        </v-row>
      </v-card>
      <v-card class="ma-5 pa-5 secondary">
        <v-card-title>
          Upload a personalised gratitude video.
        </v-card-title>
        <v-radio-group
          v-model="videoType"
          :disabled="
            gratitudeMessage.videoUrl || gratitudeMessage.videoUrl !== ''
          "
          row
        >
          <v-radio label="Video Link" value="link"></v-radio>
          <v-radio label="Upload Video" value="file"></v-radio>
        </v-radio-group>
        <v-file-input
          class="mx-auto"
          v-if="videoType === 'file'"
          accept="video/*"
          type="file"
          id="file"
          ref="file"
          label="Upload Video"
          outlined
          rounded
          v-model="videoFile"
          v-on:change="uploadOrClear"
        />
        <v-text-field
          v-if="videoType === 'link'"
          type="text"
          label="Link"
          class="mx-auto"
          rounded
          outlined
          v-model="gratitudeMessage.videoUrl"
        />
      </v-card>
      <v-card class="ma-5 pa-5 secondary">
        <v-card-title>
          Enter and describe the volunteer Call To Actions (CTA).
        </v-card-title>
        <v-card-subtitle> Maximum of three CTAs per video.git</v-card-subtitle>
        <CTAInput
          v-for="i in showCTANum()"
          :key="i"
          :cta-num="i"
          v-model="gratitudeMessage.callsToAction[i - 1]"
        >
        </CTAInput>
        <v-card-actions>
          <v-btn
            v-show="numCTAs < gratitudeMessage.callsToAction.length"
            @click="numCTAs++"
            rounded
            outlined
            class="secondary darken-2 mx-auto align-center"
            >Add CTA</v-btn
          >
        </v-card-actions>
      </v-card>
      <div class="w-full text-center md:text-right mb-8">
        <button
          data-cy="submit"
          class="w-full md:w-auto bg-gray-100 hover:bg-gray-200 text-blue-800 text-xl py-4 px-16 rounded-full shadow-lg"
          @click="submit(gratitudeMessage)"
        >
          Submit
        </button>
      </div>
    </v-form>
  </v-container>
</template>

<script>
import Vimeo from "@/services/vimeo";
import CTAInput from "./CTAInput";

export default {
  name: "GratitudeMessageForm",
  components: { CTAInput },
  model: {
    prop: "gratitude-message",
    event: "change"
  },
  props: {
    gratitudeMessage: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      descriptionMaxLength: 85,
      videoType: "link",
      videoFile: [],
      numCTAs: 1
    };
  },
  methods: {
    showCTANum: function() {
      return Math.min(this.numCTAs, this.gratitudeMessage.callsToAction.length);
    },
    uploadOrClear() {
      if (
        !this.videoFile ||
        (Array.isArray(this.videoFile) && length(this.videoFile) === 0)
      ) {
        this.gratitudeMessage.videoUrl = "";
      } else if (Array.isArray(this.videoFile)) {
        this.uploadVideo(this.videoFile[0]);
      } else {
        this.uploadVideo(this.videoFile);
      }
    },
    // updateBeneficiaryName(beneficiaryName) {
    //   this.$emit("input", {
    //     ...this.gratitudeMessage,
    //     beneficiaryName
    //   });
    // },
    // updateRecipientName(recipientName) {
    //   this.$emit("input", {
    //     ...this.gratitudeMessage,
    //     recipientName
    //   });
    // },
    // updateOrganizationName(organizationName) {
    //   this.$emit("input", {
    //     ...this.gratitudeMessage,
    //     organizationName
    //   });
    // },
    // updatePrimaryContactName(primaryContactName) {
    //   this.$emit("input", {
    //     ...this.gratitudeMessage,
    //     primaryContactName
    //   });
    // },
    // updateRecipientEmail(recipientEmail) {
    //   this.$emit("input", {
    //     ...this.gratitudeMessage,
    //     recipientEmail
    //   });
    // },
    // updateVideoUrl(videoUrl) {
    //   this.$emit("input", {
    //     ...this.gratitudeMessage,
    //     videoUrl
    //   });
    // },
    // updateCtaButtonText(index, buttonText) {
    //   let callsToAction = this.gratitudeMessage.callsToAction;
    //   callsToAction[index] = {
    //     ...this.gratitudeMessage.callsToAction[index],
    //     buttonText
    //   };
    //   this.$emit("input", {
    //     ...this.gratitudeMessage,
    //     callsToAction
    //   });
    // },
    // updateCtaLink(index, link) {
    //   let callsToAction = this.gratitudeMessage.callsToAction;
    //   callsToAction[index] = {
    //     ...this.gratitudeMessage.callsToAction[index],
    //     link
    //   };
    //   this.$emit("input", {
    //     ...this.gratitudeMessage,
    //     callsToAction
    //   });
    // },
    // updateCtaDescription(index, description) {
    //   if (description.length > this.descriptionMaxLength) {
    //     description = this.gratitudeMessage.callsToAction[
    //       index
    //     ].description.substring(0, this.descriptionMaxLength);
    //   }
    //
    //   let callsToAction = this.gratitudeMessage.callsToAction;
    //   callsToAction[index] = {
    //     ...this.gratitudeMessage.callsToAction[index],
    //     description
    //   };
    //   this.$emit("input", {
    //     ...this.gratitudeMessage,
    //     callsToAction
    //   });
    // },
    uploadVideo(file) {
      let client = new Vimeo(
        "5eae5ebb7bcd5ef29fd7df5c43a05ac66f9c9ce8",
        "XY45SWIYxKK6CNuTjfI5eHyybgqgLC47gnDikMHK/l20TI+M17lNQrnJUeK2Zbo+PEFCGILMAOF4gzXvPnVSFtM3VI46k6mFVyAIO2seuk1QfVe3I7Gv2AVQWbUm1dLY",
        "c0a6f79e7bae63b885083950efb3adff"
      );

      client.upload(
        file,
        {
          name: "The Gratitude Project",
          description: "Thank You!"
        },
        uri => {
          this.isUploaded = true;
          console.log("Your video URI is: " + uri);
          this.gratitudeMessage.videoUrl = `https://player.vimeo.com${uri}`;
        },
        (bytes_uploaded, bytes_total) => {
          var percentage = ((bytes_uploaded / bytes_total) * 100).toFixed(2);
          console.log(bytes_uploaded, bytes_total, percentage + "%");
        },
        error => {
          this.isUploading = false;
          console.log("Failed because: " + error);
        }
      );
    },
    submit(gratitudeMessage) {
      this.errors = [];

      if (gratitudeMessage.beneficiaryName.length === 0) {
        this.errors.push("Missing beneficiary name");
      }
      if (gratitudeMessage.recipientName.length === 0) {
        this.errors.push("Missing recipient name");
      }
      if (gratitudeMessage.recipientEmail.length === 0) {
        this.errors.push("Missing recipient email");
      }
      if (gratitudeMessage.videoUrl.length === 0) {
        this.errors.push("Missing video link");
      }

      if (this.errors.length > 0) {
        window.scrollTo(0, 0);
        return;
      }

      gratitudeMessage.callsToAction = gratitudeMessage.callsToAction.filter(
        cta => cta.buttonText.length > 0 && cta.link.length > 0
      );

      this.$emit("submit", this.gratitudeMessage);
    }
  }
};
</script>
<style>
.cta-input {
  @apply border-blue-200;
}
.cta-input:focus {
  @apply border-white;
}
</style>
