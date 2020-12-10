<template>
  <h1>
    Video Call<br /><small style="font-size: 14pt;">Powered by Agora.io</small>
  </h1>
  <div id="remote-container"></div>
  <button v-on:click="startCall">Join channel</button>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import AgoraRTC from "agora-rtc-sdk";

const rtc: any = {
  client: null,
  localStream: null,
  params: {},
};

export default defineComponent({
  name: "VoiceCall",
  methods: {
    startCall: async () => {
      rtc.client = AgoraRTC.createClient({ mode: "rtc", codec: "h264" });
      await rtc.client.init("ad65498ba2f5406aad676a1ed4d34230");
      await rtc.client.join(null, `my-room`, null, function(
        uid: string | number
      ) {
        rtc.params.uid = uid;
        console.log("####", uid);
        rtc.localStream = AgoraRTC.createStream({
          streamID: rtc.params.uid,
          audio: true,
          video: false,
          screen: false,
        });

        rtc.localStream.init(function() {
          rtc.client.publish(rtc.localStream, function(err: any) {
            console.error("####error in publish", err);
          });
        });

        rtc.client.on("stream-added", function(evt: any) {
          const remoteStream = evt.stream;
          const id = remoteStream.getId();
          if (id !== rtc.params.uid) {
            rtc.client.subscribe(remoteStream);
          }
        });

        rtc.client.on("stream-subscribed", function(evt: any) {
          const remoteStream = evt.stream;
          remoteStream.play("remote-container");
        });

        rtc.client.on("stream-removed", function(evt: any) {
          const remoteStream = evt.stream;
          remoteStream.stop("remote-container");
        });
      });
    },
  },
});
</script>

<style scoped></style>
