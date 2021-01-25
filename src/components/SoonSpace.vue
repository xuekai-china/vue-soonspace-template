<template>
  <div class="outside">
    <vue-soonspace
      id="vue-soonspace-customId"
      className="vue-soonspace-customClass"
      :option="{
        showInfo: true,
        showGrid: false,
        useIndexedDB: true,
        closeWarnLog: true,
      }"
      :globalSdk="true"
      @sceneReady="sceneReady"
      @modelClick="modelClick"
      @selectPosition="selectPosition"
    ></vue-soonspace>
  </div>
</template>

<script>
import SoonmanagerSync from "@soonspacejs/soonmanager-sync";

export default {
  name: "soonspace",
  components: {},
  data() {
    return {
      // 资源没上传，把自己的资源解压后放在 public/model/ 下
      proBasePath: "./model/",
      loading: true,
      poiInfo: null,
      treeData: [],
    };
  },
  methods: {
    sceneReady(ssp) {
      window.__SSP__ = ssp;
      this.getPoiInfo().then(() => {
        this.syncSoonMManager(ssp);
        this.initLoadData().then((data) => {
          console.log("场景加载完成！", data);
          console.log("==== poiInfo ====", this.poiInfo);
          this.loading = false;
          this.$ssp.flyMainViewpoint();
        });
      });
    },
    syncSoonMManager(ssp) {
      const smy = ssp.install(SoonmanagerSync);
      smy.setBaseUrl(this.proBasePath);
      smy.setGlobalSetting();
      smy.poiInfoData2Tree(this.poiInfo).then((treeData) => {
        this.treeData = treeData;
        console.log("==== treeData ====", this.treeData);
      });
    },
    initLoadData() {
      const modelData = this.poiInfo.filter((i) => i.type === "3D");
      const poiData = this.poiInfo.filter((i) => i.type === "2D");

      const modelInfo = this.formatModelInfo(modelData);
      const poiInfo = this.formatModelInfo(poiData);

      return Promise.all([this.loadModel(modelInfo), this.loadPoi(poiInfo)]);
    },
    loadModel(info) {
      console.log("loadModel", info);
      return this.$ssp.loadSbm(info);
    },
    loadPoi(info) {
      return new Promise((resolve) => resolve(info));
    },
    formatModelInfo(data) {
      return data.map((item) => {
        const {
          id,
          name,
          x,
          y,
          z,
          rotation_x,
          rotation_y,
          rotation_z,
          scale_x,
          scale_y,
          scale_z,
          filepath,
        } = item;

        const url = `${this.proBasePath}${filepath}`;

        return {
          id,
          name,
          url,
          position: {
            x,
            y,
            z,
          },
          rotation: {
            x: (Math.PI / 180) * rotation_x,
            y: (Math.PI / 180) * rotation_y,
            z: (Math.PI / 180) * rotation_z,
          },
          scale: {
            x: scale_x,
            y: scale_y,
            z: scale_z,
          },
          userData: item,
        };
      });
    },
    formatPoiInfo(data) {
      return data;
    },
    getPoiInfo() {
      return fetch(`${this.proBasePath}db/poiInfo.json`)
        .then((res) => res.json())
        .then((json) => {
          this.poiInfo = json;
          return Promise.resolve();
        });
    },
    modelClick(param) {
      console.log("soonspace modelClick", param);
    },
    selectPosition(position) {
      console.log("soonspace selectPosition", position);
    },
  },
};
</script>

<style scoped>
.outside {
  height: 100%;
  width: 100%;
}
</style>