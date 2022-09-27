<template>
  <div>
    <div class="wrapper">
      <p class="title">đơn ứng tuyển</p>
      <div class="progress-bar">
        <ul class="progress-bar-list">
          <li class="progress-bar-step" :class="{ active: step === 1 }">
            Thông tin cá nhân
          </li>
          <li class="progress-bar-step" :class="{ active: step === 2 }">
            Kinh nghiệm làm việc
          </li>
          <li class="progress-bar-step" :class="{ active: step === 3 }">
            Về công ty
          </li>
        </ul>
      </div>
      <keep-alive>
        <UserInfo
          v-if="step === 1"
          @nextStep="nextStep"
          @finishForm="addForm"
        />
        <ExperiencesComp
          v-else-if="step === 2"
          @nextStep="nextStep"
          @preStep="preStep"
          @finishForm="addForm"
        />
        <ConfirmCom v-else @preStep="preStep" @finishForm="addForm" />
      </keep-alive>
    </div>
  </div>
</template>

<script>
import UserInfo from "../components/userInfo/index.vue";
import ExperiencesComp from "../components/Experiences/ExperiencesComp.vue";
import ConfirmCom from "../components/Confirm/Confirm.vue";
export default {
  name: "ContainerApp",
  data: function () {
    return {
      step: 1,
      finalForm :{}
    };
  },
  components: {
    UserInfo,
    ConfirmCom,
    ExperiencesComp,
  },
  methods: {
    preStep() {
      this.step--;
    },
    nextStep() {
      this.step++;
    },
    addForm(data) {
      console.log(data);
      this.finalForm =Object.assign(this.finalForm, data);
      if (this.step === 3) console.log(this.finalForm);
    },
  },
};
</script>

<style lang="scss">
.wrapper {
  width: 926px;
  height: 849px;
  margin: 0 auto;

  .title {
    text-transform: uppercase;
    font-weight: 400;
    font-size: 24px;
  }

  .progress-bar {
    &-list {
      display: flex;
      list-style-type: none;
      gap: 17px;
      counter-reset: step;
      padding-left: 0px;
      margin: 16px 0px 20px 0px;
    }

    &-step {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      position: relative;
      width: 170px;

      &::before {
        content: counter(step);
        color: #ffffff;
        font-weight: 700;
        font-size: 14px;
        line-height: 20px;
        counter-increment: step;
        display: flex;
        justify-content: center;
        align-items: center;
        width: 32px;
        height: 32px;
        background: #dbdbdb;
        border-radius: 50%;
        margin-bottom: 14px;
      }

      &:first-child::after {
        content: none;
      }

      &::after {
        content: "";
        position: absolute;
        width: 100%;
        height: 1px;
        background: #dbdbdb;
        top: 19px;
        left: -50%;
        z-index: -1;
      }
    }
  }

  .active {
    &::before {
      background-color: #617d98;
      width: 40px;
      height: 40px;
    }
  }
}
</style>
