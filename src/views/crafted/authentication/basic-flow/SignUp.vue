<template>
  <!--begin::Wrapper-->
  <div class="w-lg-500px p-10">
    <!--begin::Form-->
    <VForm class="form w-100 fv-plugins-bootstrap5 fv-plugins-framework" novalidate @submit="onSubmitRegister"
      id="kt_login_signup_form" :validation-schema="registration">
      <!--begin::Heading-->
      <div class="mb-10 text-center">
        <!--begin::Title-->
        <h1 class="text-dark mb-3">Create an Account</h1>
        <!--end::Title-->

        <!--begin::Link-->
        <div class="text-gray-400 fw-semobold fs-4">
          Already have an account?

          <router-link to="/sign-in" class="link-primary fw-bold">
            Sign in here
          </router-link>
        </div>
        <!--end::Link-->
      </div>
      <!--end::Heading-->

      <!--begin::Action-->
      <button type="button" class="btn btn-light-primary fw-bold w-100 mb-10">
        <img alt="Logo" :src="getAssetPath('media/svg/brand-logos/google-icon.svg')" class="h-20px me-3" />
        Sign in with Google
      </button>
      <!--end::Action-->

      <!--begin::Separator-->
      <div class="d-flex align-items-center mb-10">
        <div class="border-bottom border-gray-300 mw-50 w-100"></div>
        <span class="fw-semobold text-gray-400 fs-7 mx-2">OR</span>
        <div class="border-bottom border-gray-300 mw-50 w-100"></div>
      </div>
      <!--end::Separator-->

      <!--begin::Input group-->
      <div class="fv-row mb-10">
        <!--begin::Label-->
        <label class="form-label fs-6 fw-bold text-dark">Username</label>
        <!--end::Label-->

        <!--begin::Input-->
        <Field tabindex="1" class="form-control form-control-lg form-control-solid" type="text" name="username"
          autocomplete="off"/>
        <!--end::Input-->
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="username" />
          </div>
        </div>
      </div>
      <!--end::Input group-->

      <!--begin::Input group-->
      <div class="fv-row mb-7">
        <label class="form-label fw-bold text-dark fs-6">Email</label>
        <Field class="form-control form-control-lg form-control-solid" type="email" placeholder="" name="email"
          autocomplete="off"/>
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="email" />
          </div>
        </div>
      </div>
      <!--end::Input group-->

      <!--begin::Input group-->
      <div class="fv-row mb-10">
        <label class="form-check form-check-custom form-check-solid">
          <Field class="form-check-input" type="checkbox" name = "checkbox" value="true"/>
          <span class="form-check-label fw-semobold text-gray-700 fs-6">
            I Agree &
            <router-link to="/termAndCon" class="link-primary fw-bold">
              Terms and conditions
          </router-link>
          </span>
        </label>
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="email" />
          </div>
        </div>
      </div>
      <!--end::Input group-->

      <!--begin::Actions-->
      <div class="text-center">
        <button id="kt_sign_up_submit" ref="submitButton" type="submit" class="btn btn-lg btn-primary">
          <span class="indicator-label"> Submit </span>
          <span class="indicator-progress">
            Please wait...
            <span class="spinner-border spinner-border-sm align-middle ms-2"></span>
          </span>
        </button>
      </div>
      <!--end::Actions-->
    </VForm>
    <!--end::Form-->
  </div>
  <!--end::Wrapper-->
</template>

<script lang="ts">
import { getAssetPath } from "@/core/helpers/assets";
import { defineComponent, nextTick, onMounted, ref } from "vue";
import { ErrorMessage, Field, Form as VForm } from "vee-validate";
import * as Yup from "yup";
import { useAuthStore, type User } from "@/stores/auth";
import { useRouter } from "vue-router";
import { PasswordMeterComponent } from "@/assets/ts/components";
import Swal from "sweetalert2/dist/sweetalert2.js";
import ApiService from "@/core/services/ApiService";

export default defineComponent({
  name: "sign-up",
  components: {
    Field,
    VForm,
    ErrorMessage,
  },
  setup() {
    const store = useAuthStore();
    const router = useRouter();

    const submitButton = ref<HTMLButtonElement | null>(null);

    const registration = Yup.object().shape({
      username: Yup.string().min(4).max(40).required().label("Username"),
      email: Yup.string().min(4).required().email().label("Email"),
      checkbox: Yup.boolean().isTrue().required().label("Terms and conditions")
    });

    onMounted(() => {
      nextTick(() => {
        PasswordMeterComponent.bootstrap();
      });
    });

    const onSubmitRegister = async (values: any) => {
      let errorData;
      let errorStatus = 200;
      let password ;

      const length = 10;
      const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
      let result = '';

  for (let i = 0; i < length; i++) {
        const randomIndex = Math.floor(Math.random() * characters.length);
        result += characters.charAt(randomIndex);
      }
      password = result;

      const data = await ApiService.post("http://202.129.16.94:82/api/register",
        {
          username: values.username,
          email: values.email,
          password: password
        }
      ).catch((error) => {
        errorData = error.response.data.error;
        errorStatus = error.response.status;
      });
      console.log("data = " + JSON.stringify(data));

      if (errorStatus === 200) {
        Swal.fire({
          html: `You have successfully logged in!<br>Your password is ${password.value}<br>--> Click OK to copy password<--`,
          icon: "success",
          buttonsStyling: false,
          confirmButtonText: "Ok and copy",
          heightAuto: false,
          customClass: {
            confirmButton: "btn fw-semobold btn-light-primary",
          },
        }).then(function () {
          navigator.clipboard.writeText(`${password.value}`);
          router.push({ name: "dashboard" });
        }
        );

      }else{
          Swal.fire({
          html:`${errorStatus}<br>${errorData}`,
          icon: "error",
          buttonsStyling: false,
          confirmButtonText: "Try again!",
          heightAuto: false,
          customClass: {
            confirmButton: "btn fw-semobold btn-light-danger",
          },
        });
      }
    };

    return {
      registration,
      onSubmitRegister,
      submitButton,
      getAssetPath,
    };
  },
});
</script>
