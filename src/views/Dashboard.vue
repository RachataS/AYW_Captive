<template>
  <!--begin::Row-->
  <div class="row g-5 g-xl-10 mb-5 mb-xl-10">

    <!--begin::Col-->
    <div class="col-xxl-8">
      <Widget5 className="h-md-100" />
    </div>
    <!--end::Col-->
  </div>
  
</template>

<script lang="ts">
import { getAssetPath } from "@/core/helpers/assets";
import { defineComponent } from "vue";
import Widget1 from "@/components/dashboard-default-widgets/Widget1.vue";
import Widget2 from "@/components/dashboard-default-widgets/Widget2.vue";
import Widget3 from "@/components/dashboard-default-widgets/Widget3.vue";
import Widget4 from "@/components/dashboard-default-widgets/Widget4.vue";
import Widget5 from "@/components/dashboard-default-widgets/Widget5.vue";
import Widget6 from "@/components/dashboard-default-widgets/Widget6.vue";
import Widget7 from "@/components/dashboard-default-widgets/Widget7.vue";
import Widget8 from "@/components/dashboard-default-widgets/Widget8.vue";
import Widget9 from "@/components/dashboard-default-widgets/Widget9.vue";
import Widget10 from "@/components/dashboard-default-widgets/Widget10.vue";
import MixedWidget5 from "@/components/widgets/mixed/Widget5.vue";
import ApiService from "@/core/services/ApiService";
import * as cheerio from "cheerio";

export default defineComponent({
  name: "main-dashboard",
  components: {
    Widget1,
    Widget2,
    Widget3,
    Widget4,
    Widget5,
    Widget6,
    Widget7,
    Widget8,
    Widget9,
    Widget10,
    MixedWidget5,
  },
  methods:{
    // async getData() {
    //   const protocol = window.location.protocol ?? "http:";
    //   const host = window.location.hostname ?? "localhost";
    //   const port = window.location.port ?? "5173";

    //   const html = await ApiService.get(`${protocol}//${host}:${port}`, "status");
    //   const $ = cheerio.load(html.data.toString());
    //   const APusername = $(`input[name = "username"]`).val() as string;
    //   const APip = $(`input[name = "ip"]`).val() as string;
    //   const APbytein = $(`input[name = "bytes-in-nice"]`).val() as string;
    //   const APbyteout = $(`input[name = "bytes-out-nice"]`).val() as string;
    //   const APuptime = $(`input[name = "uptime"]`).val() as string;
    //   this.username = APusername;
    //   this.ip = APip;
    //   this.bytein = APbytein;
    //   this.byteout = APbyteout;
    //   this.uptime = APuptime;
    // },
  },
  setup() {
    return {
      getAssetPath,
    };
  },
});
</script>
