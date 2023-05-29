<template>
  <!--begin::Wrapper-->
  <div class="w-lg-500px p-10">
    <!--begin::Form-->
    <VForm class="form w-100 fv-plugins-bootstrap5 fv-plugins-framework" @submit="onSubmitForgotPassword"
      id="kt_login_password_reset_form" :validation-schema="forgotPassword">
      <!--begin::Heading-->
      <div class="text-center mb-10">
        <!--begin::Title-->
        <h1 class="text-dark mb-3">Change Email</h1>
        <!--end::Title-->

        <!--begin::Link-->
        <div class="text-gray-400 fw-semobold fs-4">
          Enter your username, new email and password
        </div>
        <!--end::Link-->
      </div>
      <!--begin::Heading-->

      <!--begin::Input group-->
      <div class="fv-row mb-10">
        <label class="form-label fw-bold text-gray-900 fs-6">Username</label>
        <Field class="form-control form-control-solid" type="username" placeholder="" name="username" autocomplete="off"
          v-model="username" />
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="username" />
          </div>
        </div>
      </div>
      <div class="fv-row mb-10">
        <label class="form-label fw-bold text-gray-900 fs-6">Password</label>
        <Field class="form-control form-control-solid" type="password" placeholder="" name="password" autocomplete="off"
          v-model="password" />
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="password" />
          </div>
        </div>
      </div>
      <div class="fv-row mb-10">
        <label class="form-label fw-bold text-gray-900 fs-6">New email</label>
        <Field class="form-control form-control-solid" type="email" placeholder="" name="newemail" autocomplete="off"
          v-model="newemail" />
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="newemail" />
          </div>
        </div>
      </div>
      <!--end::Input group-->

      <!--begin::Actions-->
      <div class="d-flex flex-wrap justify-content-center pb-lg-0">
        <button type="submit" ref="submitButton" id="kt_password_reset_submit"
          class="btn btn-lg btn-primary fw-bold me-4">
          <span class="indicator-label"> Submit </span>
          <span class="indicator-progress">
            Please wait...
            <span class="spinner-border spinner-border-sm align-middle ms-2"></span>
          </span>
        </button>

        <router-link to="/sign-up" class="btn btn-lg btn-light-primary fw-bold">Cancel</router-link>
      </div>
      <!--end::Actions-->
    </VForm>
    <!--end::Form-->
  </div>
  <!--end::Wrapper-->
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import { ErrorMessage, Field, Form as VForm } from "vee-validate";
import { useAuthStore } from "@/stores/auth";
import * as Yup from "yup";
import Swal from "sweetalert2/dist/sweetalert2.js";
import ApiService from "@/core/services/ApiService";

export default defineComponent({
  name: "email-change",
  components: {
    Field,
    VForm,
    ErrorMessage,
  },
  data() {
    return {
      username: "",
      newemail: "",
      password: "",
    }
  },
  setup() {

    const username = ref('');
    const newemail = ref('');
    const password = ref('');
    const store = useAuthStore();

    const submitButton = ref<HTMLButtonElement | null>(null);

    //Create form validation object
    const forgotPassword = Yup.object().shape({
      username: Yup.string().min(4).max(20).label("Username"),
      newemail: Yup.string().min(4).required().email().label("New email"),
      password: Yup.string().min(4).max(20).label("New password"),
    });

    //Form submit function
    const onSubmitForgotPassword = async (values: any) => {
      let errorData;
      let errorStatus = 200;

      const data = await ApiService.vueInstance.axios.patch("http://202.129.16.94:82/api/changeEmail",
        {
          username: username.value,
          password: password.value,
          new_email: newemail.value,
        }
      ).catch((error) => {
        errorData = error.response.data.error;
        errorStatus = error.response.status;
      });

      if (errorStatus === 200) {
        Swal.fire({
          text: "You have successfully changed your email!",
          icon: "success",
          buttonsStyling: false,
          confirmButtonText: "Ok, got it!",
          heightAuto: false,
          customClass: {
            confirmButton: "btn fw-semobold btn-light-primary",
          },
        })
      } else {
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
      // values = values as string;

      // // eslint-disable-next-line
      // submitButton.value!.disabled = true;
      // // Activate loading indicator
      // submitButton.value?.setAttribute("data-kt-indicator", "on");

      // // dummy delay
      // // Send login request
      // await store.forgotPassword(values);

      // const error = Object.values(store.errors);

      // if (!error) {
      //   Swal.fire({
      //     text: "You have successfully logged in!",
      //     icon: "success",
      //     buttonsStyling: false,
      //     confirmButtonText: "Ok, got it!",
      //     heightAuto: false,
      //     customClass: {
      //       confirmButton: "btn fw-semobold btn-light-primary",
      //     },
      //   });
      // } else {
      //   Swal.fire({
      //     text: error[0] as string,
      //     icon: "error",
      //     buttonsStyling: false,
      //     confirmButtonText: "Try again!",
      //     heightAuto: false,
      //     customClass: {
      //       confirmButton: "btn fw-semobold btn-light-danger",
      //     },
      //   });
      // }

      // submitButton.value?.removeAttribute("data-kt-indicator");
      // // eslint-disable-next-line
      //   submitButton.value!.disabled = false;
    };

    return {
      username,
      newemail,
      password,
      onSubmitForgotPassword,
      forgotPassword,
      submitButton,
    };
  },
});
</script>