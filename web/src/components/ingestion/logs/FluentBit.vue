<!-- Copyright 2022 Zinc Labs Inc. and Contributors

 Licensed under the Apache License, Version 2.0 (the "License");
 you may not use this file except in compliance with the License.
 You may obtain a copy of the License at

     http:www.apache.org/licenses/LICENSE-2.0

 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License. 
-->

<template>
  <div class="tabContent q-ma-md">
    <div class="tabContent__head">
      <div class="title" data-test="fluent-bit-title-text">Fluent Bit</div>
      <div class="copy_action">
        <q-btn
          data-test="fluent-bit-copy-btn"
          flat
          round
          size="0.5rem"
          padding="0.6rem"
          :icon="'img:' + getImageURL('images/common/copy_icon.svg')"
          @click="$emit('copy-to-clipboard-fn', fluentbitContent)"
        />
      </div>
    </div>
    <pre ref="fluentbitContent" data-test="fluent-bit-content-text">
[OUTPUT]
  Name http
  Match *
  URI /api/{{ currOrgIdentifier }}/default/_json
  Host {{ endpoint.host }}
  Port {{ endpoint.port }}
  tls {{ endpoint.tls }}
  Format json
  Json_date_key    {{ store.state.zoConfig.timestamp_column }}
  Json_date_format iso8601
  HTTP_User {{ currUserEmail }}
  HTTP_Passwd {{ store.state.organizationPasscode }}</pre
    >
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, type Ref } from "vue";
import config from "../../../aws-exports";
import { useStore } from "vuex";
import { getImageURL } from "../../../utils/zincutils";
import type { Endpoint } from "@/ts/interfaces";
export default defineComponent({
  name: "fluentbit-mechanism",
  props: {
    currOrgIdentifier: {
      type: String,
    },
    currUserEmail: {
      type: String,
    },
  },
  setup() {
    const store = useStore();
    const endpoint: Ref<Endpoint> = ref({
      url: "",
      host: "",
      port: "",
      protocol: "",
      tls: "",
    });
    const url = new URL(store.state.API_ENDPOINT);
    endpoint.value = {
      url: store.state.API_ENDPOINT,
      host: url.hostname,
      port: url.port || (url.protocol === "https:" ? "443" : "80"),
      protocol: url.protocol.replace(":", ""),
      tls: url.protocol === "https:" ? "On" : "Off",
    };
    const fluentbitContent = ref(null);
    return {
      store,
      config,
      endpoint,
      fluentbitContent,
      getImageURL,
    };
  },
});
</script>

<style scoped lang="scss">
.tabContent {
  background-color: $accent; // tab content bg color
  padding: 1rem 1.25rem 0.5rem;
  border-radius: 0.5rem;
  &__head {
    justify-content: space-between;
    text-transform: uppercase;
    align-items: center;
    display: flex;
    .title {
      font-size: 0.75rem;
      color: $dark-page;
      line-height: 1rem;
      font-weight: 600;
    }
    .copy_action {
      .q-btn {
        background-color: white;
      }
    }
  }
  pre {
    white-space: pre-wrap;
    word-wrap: break-word;
    font-size: 0.75rem;
    color: $dark-page;
    margin-bottom: 0;
  }
}
</style>
