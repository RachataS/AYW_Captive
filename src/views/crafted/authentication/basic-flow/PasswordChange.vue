<template>
  <!--begin::Wrapper-->
  <div class="w-lg-500px p-10">
    <!--begin::Form-->
    <VForm
      class="form w-100 fv-plugins-bootstrap5 fv-plugins-framework"
      @submit="onSubmitForgotPassword"
      id="kt_login_password_reset_form"
      :validation-schema="forgotPassword"
    >
      <!--begin::Heading-->
      <div class="text-center mb-10">
        <!--begin::Title-->
        <h1 class="text-dark mb-3">Change password</h1>
        <!--end::Title-->

        <!--begin::Link-->
        <div class="text-gray-400 fw-semobold fs-4">
          Enter your Username,Old password and new password
        </div>
        <!--end::Link-->
      </div>
      <!--begin::Heading-->

      <!--begin::Input group-->
      <div class="fv-row mb-10">
        <label class="form-label fw-bold text-gray-900 fs-6">Username</label>
        <Field
          class="form-control form-control-solid"
          type="username"
          placeholder=""
          name="username"
          autocomplete="off"
          v-model="username"
        />
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="username" />
          </div>
        </div>
      </div>
      <div class="fv-row mb-10">
        <label class="form-label fw-bold text-gray-900 fs-6">Old password</label>
        <Field
          class="form-control form-control-solid"
          type="password"
          placeholder=""
          name="oldpassword"
          autocomplete="off"
          v-model="oldpassword"
        />
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="oldpassword" />
          </div>
        </div>
      </div>
      <div class="fv-row mb-10">
        <label class="form-label fw-bold text-gray-900 fs-6">New password</label>
        <Field
          class="form-control form-control-solid"
          type="password"
          placeholder=""
          name="newpassword"
          autocomplete="off"
          v-model="newpassword"
        />
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="newpassword" />
          </div>
        </div>
      </div>
      <div class="fv-row mb-10">
        <label class="form-label fw-bold text-gray-900 fs-6">Confirm password</label>
        <Field
          class="form-control form-control-solid"
          type="password"
          placeholder=""
          name="conpassword"
          autocomplete="off"
          v-model="conpassword"
        />
        <div class="fv-plugins-message-container">
          <div class="fv-help-block">
            <ErrorMessage name="conpassword" />
          </div>
        </div>
      </div>
      <!--end::Input group-->

      <!--begin::Actions-->
      <div class="d-flex flex-wrap justify-content-center pb-lg-0">
        <button
          type="submit"
          ref="submitButton"
          id="kt_password_reset_submit"
          class="btn btn-lg btn-primary fw-bold me-4"
        >
          <span class="indicator-label"> Submit </span>
          <span class="indicator-progress">
            Please wait...
            <span
              class="spinner-border spinner-border-sm align-middle ms-2"
            ></span>
          </span>
        </button>

        <router-link to="/dashboard" class="btn btn-lg btn-light-primary fw-bold"
          >Cancel</router-link
        >
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
  name: "password-change",
  components: {
    Field,
    VForm,
    ErrorMessage,
  },data (){
    return{
      username:"",
      oldpassword:"",
      newpassword:"",
      conpassword:"",
    }
  },
  setup() {

    const username = ref('');
    const oldpassword = ref('');
    const newpassword = ref('');
    const conpassword = ref('');

    const store = useAuthStore();

    const submitButton = ref<HTMLButtonElement | null>(null);

    //Create form validation object
    const forgotPassword = Yup.object().shape({
      username : Yup.string().min(4).max(20).label("Username"),
      oldpassword : Yup.string().min(4).max(20).label("Old password"),
      newpassword : Yup.string().min(4).max(20).label("New password"),
      conpassword: Yup.string()
        .required()
        .oneOf([Yup.ref("newpassword"), null], "Passwords must match")
        .label("Password Confirmation"),
    });

    //Form submit function
    const onSubmitForgotPassword = async (values: any) => {

      let errorData;
      let errorStatus = 200;

      const data = await ApiService.vueInstance.axios.patch("http://202.129.16.94:82/api/changePassword",
        {
          username : username.value,
          password : oldpassword.value,
          new_password : newpassword.value,
        }
      ).catch((error) => {
        errorData = error.response.data.error;
        errorStatus = error.response.status;
      });

      console.log(`username = ${username.value}\npassword = ${oldpassword.value}\nnew password = ${newpassword.value}`);
      console.log(`error status = ${errorStatus}\nerror data = ${errorData}`)

      if (errorStatus === 200) {
        Swal.fire({
          text: "You have successfully changed your password!",
          icon: "success",
          buttonsStyling: false,
          confirmButtonText: "Ok, got it!",
          heightAuto: false,
          customClass: {
            confirmButton: "btn fw-semobold btn-light-primary",
          },
        })
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
      onSubmitForgotPassword,
      forgotPassword,
      submitButton,
      username,
      oldpassword,
      newpassword,
      conpassword,
    };
  },
});
</script>
