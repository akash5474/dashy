<template>
<div class="airvpn-wrapper" v-if="airvpnInfo">
  <h4 v-if="title">{{ title }}</h4>
  <p v-if="airvpnInfo.isVpn" class="status connected">
    <AirvpnIcon class="tile-icon large"/>
    Connected
  </p>
  <p v-else class="status not-connected">
    <AirvpnIcon class="tile-icon large"/>
    Not Connected
  </p>
  <div class="connection-info">
    <p><span class="lbl">IP4</span><span class="val">{{ airvpnInfo.ip }}</span></p>
    <p v-if="airvpnInfo.host">
      <span class="lbl">Host</span><span class="val">{{ airvpnInfo.host }}</span>
    </p>
    <p><span class="lbl">Location</span><span class="val">{{ airvpnInfo.location }}</span></p>
  </div>
</div>
</template>

<script>
import AirvpnIcon from './airvpn-icon.svg';
import WidgetMixin from '@/mixins/WidgetMixin';
import { widgetApiEndpoints } from '@/utils/defaults';

export default {
  mixins: [WidgetMixin],
  components: {
    AirvpnIcon,
  },
  computed: {
    endpoint() {
      return widgetApiEndpoints.airvpn;
    },
    title() {
      return this.options.title || null;
    },
  },
  data() {
    return {
      airvpnInfo: null,
    };
  },
  methods: {
    /* Make GET request to AirVPN API endpoint */
    fetchData() {
      this.makeRequest(`${this.endpoint}/?_=${Date.now()}`).then(this.processData);
    },
    /* Assign data variables to the returned data */
    processData(airvpnApiRes) {
      this.airvpnInfo = {
        ip: airvpnApiRes.ip,
        isVpn: airvpnApiRes.airvpn,
        host: airvpnApiRes.server_name,
        location: airvpnApiRes.geo.name,
      };
    },
  },
};
</script>

<style scoped lang="scss">
.airvpn-wrapper {
  color: var(--widget-text-color);
  cursor: default;

  h4 {
    display: flex;
    max-width: 250px;
    font-size: 1.2rem;
    align-items: center;
    margin: 0.25rem auto 0.5rem;
    justify-content: center;
  }
  .status {
    display: flex;
    max-width: 250px;
    font-size: 1.5rem;
    font-weight: bold;
    align-items: center;
    margin: 0.25rem auto;
    justify-content: space-evenly;
    span {
      font-size: 1.5rem;
      border-radius: 1.5rem;
      padding: 0.3rem 0.7rem;
      border: 1px solid;
      color: var(--background);
    }
    &.not-connected {
      color: var(--danger);
    }
    &.connected {
      color: var(--success);
    }
  }
  .connection-info {
    p {
      display: flex;
      max-width: 250px;
      font-size: 0.9rem;
      padding: 0.2rem;
      margin: 0.2rem auto;
      justify-content: space-between;
      opacity: var(--dimming-factor);
      span {
        &.lbl {
          font-weight: bold;
        }
        &.val {
          font-family: monospace;
        }
      }
      &:not(:last-child) { border-bottom: 1px dashed var(--widget-text-color); }
    }
  }
}

</style>
