<template></template>

<script>
import L from 'leaflet'
import 'leaflet.vectorgrid'
import { LMap } from 'vue2-leaflet'

if (typeof(L) === "undefined") {
    throw new Error("leaflet library must be installed in order " +
                    "to use vue2-leaflet-vectorgrid.")
}
if (typeof(L.vectorGrid) === "undefined") {
    throw new Error("leaflet.vectorgrid library must be installed " +
                    "in order to use vue2-leaflet-vectorgrid.")
}
if (typeof(LMap) === "undefined") {
    throw new Error("vue2-leaflet Vue Component must be installed " +
                    "in order to use vue2-leaflet-vectorgrid.")
}

export default {
  props: {
    url: {
      type: String,
      required: true
    },
    options: {
      type: Object,
      default: function () {
        return {}
      }
    }
  },
  watch: {
    url() {
      this.updateLayer()
    },
    options() {
      this.updateLayer()
    },
  },
  mounted () {
    this.mapObject = L.vectorGrid.protobuf(this.url, this.options)

    if (this.$parent._isMounted)  {
      this.deferredMountedTo(this.$parent.mapObject);
    }
  },
  beforeDestroy() {
    this.removeLayer()
  },
  methods: {
    deferredMountedTo(parent) {
      this.mapObject.addTo(parent);

      this.attributionControl = parent.attributionControl;
      for (var i = 0; i < this.$children.length; i++) {
        if (typeof this.$children[i].deferredMountedTo === "function") {
          this.$children[i].deferredMountedTo(this.mapObject);
        }
      }
    },

    setAttribution(val, old) {
      this.attributionControl.removeAttribution(old);
      this.attributionControl.addAttribution(val);
    },

    setToken(val) {
      this.options.token = val;
    },
    removeLayer() {
      this.$parent.mapObject.removeLayer(this.mapObject);
    },
    updateLayer() {
      this.removeLayer()
      this.mapObject = L.vectorGrid.protobuf(this.url, this.options)
      this.deferredMountedTo(this.$parent.mapObject);
    }
  }
}
</script>
