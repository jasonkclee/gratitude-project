<template>
  <v-container class="mx-auto">
    <v-form ref="createMessageForm" v-model="formValid">
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
            :rules="rules.nameRules"
            outlined
            rounded
            required
          />
          <v-text-field
            type="text"
            class="mx-2"
            v-model="gratitudeMessage.recipientName"
            label="Recipient Name"
            :rules="rules.nameRules"
            outlined
            rounded
            required
          />
          <v-text-field
            type="email"
            label="Email"
            class="mx-2"
            outlined
            rounded
            v-model="gratitudeMessage.recipientEmail"
            :rules="rules.emailRules"
            required
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
          :rules="rules.urlRules"
        />
        <v-text-field
          v-if="videoType === 'link'"
          type="text"
          label="Link"
          class="mx-auto"
          rounded
          outlined
          :rules="rules.urlRules"
          v-model="gratitudeMessage.videoUrl"
        />
      </v-card>
      <v-card class="ma-5 pa-5 secondary">
        <v-card-title>
          Enter any desired volunteer Call to Action(s) (CTAs).
        </v-card-title>
        <v-card-subtitle
          >You may add up to three CTAs per video.</v-card-subtitle
        >
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
      <v-row>
        <v-btn
          class="v-size--x-large mx-auto my-auto secondary"
          type="submit"
          rounded
          outlined
          :disabled="!formValid"
          @click="submit()"
        >
          Submit
        </v-btn>
      </v-row>
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
      numCTAs: 0,
      formValid: false,
      rules: {
        nameRules: [v => !!v || "Name is required"],
        emailRules: [
          v => !!v || "E-mail is required",
          v => /.+@.+\..+/.test(v) || "E-mail must be valid"
        ],
        urlRules: [
          v => !!this.gratitudeMessage.videoUrl || "Must submit video.",
          v =>
            this.gratitudeMessage.videoUrl.length > 0 || "Must submit video.",
          // eslint-disable-next-line no-useless-escape
          v =>
            /^(?:(?:https?|ftp):\/\/)?(?:(?!(?:10|127)(?:\.\d{1,3}){3})(?!(?:169\.254|192\.168)(?:\.\d{1,3}){2})(?!172\.(?:1[6-9]|2\d|3[0-1])(?:\.\d{1,3}){2})(?:[1-9]\d?|1\d\d|2[01]\d|22[0-3])(?:\.(?:1?\d{1,2}|2[0-4]\d|25[0-5])){2}(?:\.(?:[1-9]\d?|1\d\d|2[0-4]\d|25[0-4]))|(?:(?:[a-z\u00a1-\uffff0-9]-*)*[a-z\u00a1-\uffff0-9]+)(?:\.(?:[a-z\u00a1-\uffff0-9]-*)*[a-z\u00a1-\uffff0-9]+)*(?:\.(?:[a-z\u00a1-\uffff]{2,})))(?::\d{2,5})?(?:\/\S*)?$/.test(
              this.gratitudeMessage.videoUrl
            ) || "URL must be valid."
        ]
      }
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
      gratitudeMessage.callsToAction = gratitudeMessage.callsToAction.filter(
        cta => cta.buttonText.length > 0 && cta.link.length > 0
      );

      // disable submit for now
      // this.$emit("submit", this.gratitudeMessage);
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
.v-card__text,
.v-card__title {
  word-break: normal !important;
}
</style>
