<template>
  <q-item :highlight="$q.platform.is.desktop" :class="[model ? 'active': '', device.messages_ttl ? '' : 'disabled']" class="cursor-pointer" @click="deviceClickHandler">
    <q-tooltip v-if="!device.messages_ttl">You should set messages ttl more than 0</q-tooltip>
    <q-item-side :class="[model ? 'text-primary': '']" class="text-center" icon="developer_board"><q-item-tile><small>#{{device.id}}</small></q-item-tile></q-item-side>
    <q-item-main>
      <q-item-tile class="ellipsis" label>{{device.name || '&lt;noname&gt;'}}</q-item-tile>
      <small>
        <q-item-tile class="ellipsis" sublabel v-if="device.ident"><q-icon name="label_outline" /> {{device.ident}}</q-item-tile>
        <q-item-tile sublabel v-if="device.phone"><q-icon name="phone" /> {{device.phone}}</q-item-tile>
      </small>
    </q-item-main>
    <q-item-side class="text-center">
      <q-item-tile>
        <q-icon :class="[isDeviceWatched && activeDevicesID.includes(device.id) ? 'icon__send-active' : '']" size="1.5rem" name="gps_fixed" @click.stop="watchDeviceHandler">
          <q-tooltip v-model="watchTooltip" v-if="device.messages_ttl">Show on map</q-tooltip>
        </q-icon>
      </q-item-tile>
    </q-item-side>
  </q-item>
</template>

<script>
  import { QItem, QItemMain, QItemSide, QItemTile, QIcon, QTooltip } from 'quasar-framework'
  export default {
    props: [
      'device',
      'activeDevicesID',
      'isDeviceWatched'
    ],
    data () {
      return {
        watchTooltip: false
      }
    },
    computed: {
      model () {
        return this.activeDevicesID.includes(this.device.id)
      }
    },
    methods: {
      deviceClickHandler () {
        if (this.activeDevicesID.includes(this.device.id)) {
          this.unsetActiveDevice()
        }
        else {
          this.setActiveDevice()
        }
      },
      setActiveDevice () {
        if (this.device.messages_ttl) {
          this.$store.commit('setActiveDevice', this.device.id)
        }
      },
      unsetActiveDevice () {
        this.$store.commit('unsetActiveDevice', this.device.id)
      },
      watchDeviceHandler () {
        if (!this.device.messages_ttl) {
          return false
        }
        if (!this.activeDevicesID.includes(this.device.id)) {
          this.setActiveDevice()
        }
        this.isDeviceWatched ? this.$emit('update:watch-by-id', null) : this.$emit('update:watch-by-id', this.device.id)
        setTimeout(() => { this.watchTooltip = false }, 500)
      }
    },
    components: { QItem, QItemMain, QItemSide, QItemTile, QIcon, QTooltip }
  }
</script>

<style>
  .icon__send-active {
    color: #333;
  }
</style>
