<template>
  <!--begin::Wrapper-->
  <div class="w-lg-500px p-10">
    <!--begin::Form-->
    <VForm class="form w-100" id="kt_login_signin_form" @submit="onSubmitLogin" :validation-schema="login"
      :initial-values="{ username: '', password: '' }">
      <!--begin::Heading-->
      <div class="text-center mb-10">
        <!--begin::Title-->
        <h1 class="text-dark mb-3">Sign In</h1>
        <!--end::Title-->

        <!--begin::Link-->
        <div class="text-gray-400 fw-semobold fs-4">
          New Here?

          <router-link to="/sign-up" class="link-primary fw-bold">
            Create an Account
          </router-link>
        </div>
        <!--end::Link-->
      </div>

      <!--begin::Input group-->
      <div class="fv-row mb-10">
        <!--begin::Label-->
        <label class="form-label fs-6 fw-bold text-dark">Username</label>
        <!--end::Label-->

        <!--begin::Input-->
        <Field tabindex="1" class="form-control form-control-lg form-control-solid" type="text" name="username"
          autocomplete="off" />
        <!--end::Input-->
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="username" />
          </div>
        </div>
        <!--        <div class="d-flex flex-stack mb-2">
          <router-link to="/email-change" class="link-primary fs-6 fw-bold">
            Change Email?
          </router-link>
        </div>-->
      </div>
      <!--end::Input group-->

      <!--begin::Input group-->
      <div class="fv-row mb-10">
        <!--begin::Wrapper-->
        <div class="d-flex flex-stack mb-2">
          <!--begin::Label-->
          <label class="form-label fw-bold text-dark fs-6 mb-0">Password</label>
        </div>
        <Field tabindex="2" class="form-control form-control-lg form-control-solid" type="password" name="password"
          autocomplete="off" />
        <!--end::Input-->
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="password" />
          </div>
        </div>
        <div class="d-flex flex-stack mb-2">
          <!--<router-link to="/password-reset" class="link-primary fs-6 fw-bold">
            Forgot Password ?
          </router-link>-->
          <router-link to="/password-change" class="link-primary fs-6 fw-bold">
            Change Password ?
          </router-link>
        </div>
      </div>
      <!--end::Input group-->
      <div class="fv-row mb-10">

      </div>
      <!--begin::Actions-->
      <div class="text-center">
        <!--begin::Submit button-->
        <button tabindex="3" type="submit" ref="submitButton" id="kt_sign_in_submit"
          class="btn btn-lg btn-primary w-100 mb-5">
          <span class="indicator-label"> Continue </span>

          <span class="indicator-progress">
            Please wait...
            <span class="spinner-border spinner-border-sm align-middle ms-2"></span>
          </span>
        </button>
        <!--end::Submit button-->

        <!--begin::Separator-->
        <div class="text-center text-muted text-uppercase fw-bold mb-5">or</div>
        <!--end::Separator-->
        <!--begin::Google link-->

        <!--end::Google link-->
      </div>
      <!--end::Actions-->
    </VForm>
    <!--end::Form-->
    <div>
      <!-- <a href="#" class="btn btn-flex flex-center btn-light btn-lg w-100 mb-5">
        <img alt="Logo" :src="getAssetPath('media/svg/brand-logos/google-icon.svg')" class="h-20px me-3" />
        Continue with Google
      </a> -->
      <div class="button-container btn btn-flex flex-center btn-light btn-lg w-100 mb-5">
        <GoogleLogin class="button-1" style="opacity: 0; width: 200px;" :callback="callback">
        </GoogleLogin>
        <div class="button2">
          <img alt="Logo" :src="getAssetPath('media/svg/brand-logos/google-icon.svg')" class="h-20px me-3" />
          Continue with Google
        </div>
      </div>
      <!--begin::Google link-->
      <!-- <v-facebook-login app-id="1326150037969654"></v-facebook-login> -->

      <a href="#" class="btn btn-flex flex-center btn-light btn-lg w-100 mb-5">
        <img alt="Logo" :src="getAssetPath('media/svg/brand-logos/facebook-4.svg')" class="h-20px me-3" />
        Continue with Facebook
      </a>
      <a href="#" class="btn btn-flex flex-center btn-light btn-lg w-100">
        <img alt="Logo" :src="getAssetPath('media/svg/brand-logos/apple-black.svg')" class="h-20px me-3" />
        Continue with Apple
      </a>
    </div>
  </div>
  <!--end::Wrapper-->
</template>



<script lang="ts">
import { getAssetPath } from "@/core/helpers/assets";
import { defineComponent, inject, ref } from "vue";
import { ErrorMessage, Field, Form as VForm } from "vee-validate";
import { useAuthStore, type User } from "@/stores/auth";
import { useRouter } from "vue-router";
import Swal from "sweetalert2/dist/sweetalert2.js";
import * as Yup from "yup";
import ApiService from "@/core/services/ApiService";
import { processExpression } from "@vue/compiler-core";
import * as cheerio from "cheerio";
import router from "@/router";
import * as md5 from "@/core/plugins/md5";
import { decodeCredential, googleSdkLoaded } from 'vue3-google-login'
//import VFacebookLogin from 'vue-facebook-login-component-next'

const chapID = ref("");
const chapChallenge = ref("");

export default defineComponent({
  name: "sign-in",
  components: {
    Field,
    VForm,
    ErrorMessage,
    //VFacebookLogin,
  },
  methods: {
    callback: async (response) => {
      const protocol = window.location.protocol ?? "https:";
      const host = window.location.hostname ?? "localhost";
      const port = window.location.port ?? "5173";

      const password = 'GoogleLoginCaptive';

      let errorData;
      let errorStatus = 200;

      const passwordEncoded = md5.hexMD5(
        chapID.value + password + chapChallenge.value
      );

      console.log("login success");
      console.log(JSON.stringify(response.credential));
      const GoogleUser = decodeCredential(response.credential);
      console.log(GoogleUser);

try{      const data = await ApiService.post("http://202.129.16.94:82/api/register",
        {
          username: GoogleUser.given_name,
          email: GoogleUser.email,
          password: password
        }
      ).catch((error) => {
        errorData = error.response.data.error;
        errorStatus = error.response.status;
      });
      console.log("data = " + JSON.stringify(data));}catch(e){
        console.log("you have an account");
      }

      if (errorStatus === 200) {
        try {
          let html = await ApiService.vueInstance.axios.post(`${protocol}//${host}:${port}/apapi/login`,
            {
              username: GoogleUser.given_name,
              password: passwordEncoded,
              dst: "",
              popup: true,
            },
            {
              headers: {
                "Content-Type": "application/x-www-form-urlencoded",
              },
              withCredentials: false,
            }
          );
        } catch (e) {
          await router.push({ name: "400" });
        }

        Swal.fire({
          html: `You have successfully logged in!<br>Your password is ${password.value}<br>--> Click OK to go to dashboard<--`,
          icon: "success",
          buttonsStyling: false,
          confirmButtonText: "Ok",
          heightAuto: false,
          customClass: {
            confirmButton: "btn fw-semobold btn-light-primary",
          },
        }).then((result) => {
          if (result.isConfirmed) {
            //router.push({ name: "dashboard" });
            window.location.reload();
          }
        });
      } else {
        if (errorStatus === 400) {
          try {
            let html = await ApiService.vueInstance.axios.post(`${protocol}//${host}:${port}/apapi/login`,
              {
                username: GoogleUser.given_name,
                password: passwordEncoded,
                dst: "",
                popup: true,
              },
              {
                headers: {
                  "Content-Type": "application/x-www-form-urlencoded",
                },
                withCredentials: false,
              }
            );
          } catch (e) {
            await router.push({ name: "400" });
          }

          Swal.fire({
            html: `You have successfully logged in!<br>Your password is ${password.value}<br>--> Click OK to go to dashboard<--`,
            icon: "success",
            buttonsStyling: false,
            confirmButtonText: "Ok",
            heightAuto: false,
            customClass: {
              confirmButton: "btn fw-semobold btn-light-primary",
            },
          }).then((result) => {
            if (result.isConfirmed) {
              //router.push({ name: "dashboard" });
              window.location.reload();
            }
          });
        }
        else {
          Swal.fire({
            html: `${errorStatus}<br>${errorData}`,
            icon: "error",
            buttonsStyling: false,
            confirmButtonText: "Try again!",
            heightAuto: false,
            customClass: {
              confirmButton: "btn fw-semobold btn-light-danger",
            },
          });
        }
      }
    }
  },
  setup() {
    const store = useAuthStore();
    const router = useRouter();
    const Vue3GoogleOauth = inject('Vue3GoogleOauth');

    const submitButton = ref<HTMLButtonElement | null>(null);

    //Create form validation object
    const login = Yup.object().shape({
      username: Yup.string().min(4).max(40).required().label("Username"),
      password: Yup.string().min(4).max(40).required().label("Password"),
    });

    //Form submit function
    const onSubmitLogin = async (values: any) => {
      let errorRaw;
      const protocol = window.location.protocol ?? "https:";
      const host = window.location.hostname ?? "localhost";
      const port = window.location.port ?? "5173";

      console.log(`value = ${JSON.stringify(values)}`);
      const passwordEncoded = md5.hexMD5(
        chapID.value + values.password + chapChallenge.value
      );
      console.log('password encoded = ' + passwordEncoded);
      try {
        let html = await ApiService.vueInstance.axios.post(`${protocol}//${host}:${port}/apapi/login`,
          {
            username: values.username,
            password: passwordEncoded,
            dst: "",
            popup: true,
          },
          {
            headers: {
              "Content-Type": "application/x-www-form-urlencoded",
            },
            withCredentials: false,
          }
        );

        const $ = cheerio.load(html.data.toString());
        errorRaw = $(`input[name = "error"]`).val() as string;
        console.log("errorRaw = " + JSON.stringify(errorRaw));
        if (typeof errorRaw === "undefined") {
          const chapIdraw = $(`input[name = "chap-id"]`).val() as string;
          console.log("chapIDRaw = " + JSON.stringify(chapIdraw));
          if (typeof chapIdraw === "undefined") {
            Swal.fire({
              text: "You have successfully logged in!",
              icon: "success",
              buttonsStyling: false,
              confirmButtonText: "Ok, got it!",
              heightAuto: false,
              customClass: {
                confirmButton: "btn fw-semobold btn-light-primary",
              },
            }).then(() => {
              // Go to page after successfully login
              router.push({ name: "dashboard" });
            });
          } else {
            Swal.fire({
              html: `Error! <br>${errorRaw}`,
              icon: "error",
              buttonsStyling: false,
              confirmButtonText: "Ok, got it!",
              heightAuto: false,
              customClass: {
                confirmButton: "btn fw-semobold btn-light-primary",
              },
            });
            getchap();
          }
        } else {
          if (errorRaw === "Accept") {
            Swal.fire({
              html: `Error! <br>Wrong Password`,
              icon: "error",
              buttonsStyling: false,
              confirmButtonText: "Try again",
              heightAuto: false,
              customClass: {
                confirmButton: "btn fw-semobold btn-light-primary",
              },
            });
            getchap();
            console.log(`chapID = ${chapID.value}\nchapCha = ${chapChallenge.value}`);
          } else if (errorRaw === "invalid username or password") {
            Swal.fire({
              html: `Error! <br>${errorRaw}`,
              icon: "error",
              buttonsStyling: false,
              confirmButtonText: "Try again",
              heightAuto: false,
              customClass: {
                confirmButton: "btn fw-semobold btn-light-primary",
              },
            });
            getchap();
            console.log(`chapID = ${chapID.value}\nchapCha = ${chapChallenge.value}`);
          } else {
            Swal.fire({
              html: `Error! <br>${errorRaw}`,
              icon: "error",
              buttonsStyling: false,
              confirmButtonText: "Try again",
              heightAuto: false,
              customClass: {
                confirmButton: "btn fw-semobold btn-light-primary",
              },
            });
            getchap();
          }
        }
      } catch (e) {
        await router.push({ name: "400" });
      }
    };

    return {
      onSubmitLogin,
      login,
      submitButton,
      getAssetPath,
      Vue3GoogleOauth,
    }
  },

  async mounted() {
    getchap();
  }
});

async function getchap() {
  const protocol = window.location.protocol ?? "https:";
  const host = window.location.hostname ?? "localhost";
  const port = window.location.port ?? "5173";

  try {
    const html = await ApiService.get(`${protocol}//${host}:${port}`, `apapi/login`);
    const $ = cheerio.load(html.data.toString());
    const chapIdraw = $(`input[name = "chap-id"]`).val() as string;
    console.log("chapIDRaw = " + JSON.stringify(chapIdraw));
    if (typeof chapIdraw === "undefined") {
      await router.push({ name: "dashboard" })
      return;
    }
    const chapIdOctals = chapIdraw.split("\\");
    const chapIdCode = parseInt(chapIdOctals[1], 8);
    chapID.value = String.fromCharCode(chapIdCode);

    const chapChallengeRaw = $(
      'input[name = "chap-challenge"]'
    ).val() as string;

    const chapChallengeOctals = chapChallengeRaw.split("\\");
    const chapChallengeCodes = [] as number[];
    for (let i = 1; i < chapChallengeOctals.length; i++) {
      const code = parseInt(chapChallengeOctals[i], 8);
      chapChallengeCodes.push(code);
    }
    chapChallenge.value = String.fromCharCode(...chapChallengeCodes);
    console.log("chap id = " + chapIdraw);
    console.log("chap challenge = " + chapChallengeRaw);
  }
  catch (e) {
    console.log("error", JSON.stringify(e));
    //await router.push({ name: "400" });
  }
}
</script>
<style>
.button-container {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 6.5vh; /* Adjust the height as needed */
}

.button-1,
.button-2 {
  position: absolute;
}
</style>