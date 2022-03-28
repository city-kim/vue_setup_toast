<script setup lang="ts">
import { ref, Ref, onMounted, onUnmounted, reactive, watch } from 'vue'

const props = defineProps({
  id: String,
  duration: Number,
  title: String,
  contents: String,
  isClose: {
    type: Boolean,
    default: true
  },
})

const toastData = reactive({
  isShow: false, // visible 여부
  timer: null, // 토스트 타이머
});

onMounted(() => {
  startTimer(); // 마운트되면 타이머를 돌려줌
  toastData.isShow = true; // isShow는 true값
});

onUnmounted(() => {
  clearTimeout(toastData.timer); // 언마운트시 타이머 클리어해줌
});

const startTimer = () => {
  let duration = 2000 // 기본 = 2초
  if (props.duration > 0) {
    // props로 시간이 정해졌다면
    duration = props.duration // 해당시간으로 값을 세팅해줌
  }
  toastData.timer = setTimeout(() => {
    if (!closed.value) {
      // 시간이 끝나고 closed가 되지않았다면
      close(); // 닫아줌
    }
  }, duration);
};

const closed = ref(false); // 닫는역할하는 변수
const close = () => {
  // 닫는 함수가 호출될경우
  closed.value = true; // 닫아줌
  toastData.timer = null; // 타이머도 초기화
};
const toastWrap = document.getElementById('__toast__wrap');

watch(closed, (newVal: Ref) => {
  // closed값이 변경될경우
  if (newVal) {
    toastData.isShow = false; // 토스트 숨겨줌
    setTimeout(() => {
      // 0.5초 지연 후 element도 삭제 (fade 등을 구현할때 사용)
      toastWrap.removeChild(document.getElementById(props.id));
    }, 500);
  }
});
</script>

<template>
  <transition
    leave-from-class="opacity-100"
    leave-active-class="transition-opacity"
    leave-to-class="opacity-0"
  >
    <div class="w-96 mt-3 border border-gray-300 rounded shadow bg-white"
      v-if="toastData.isShow"
    >
      <div class="flex py-2.5 px-3.5 justify-between items-center">
        <h3>{{props.title}}</h3>
        <div class="flex justify-between items-center">
          <p class="text-sm">TODO: 7 second ago</p>
          <span class="w-6 stroke-1 cursor-pointer"
            @click="close()"
          >X</span>
        </div>
      </div>
      <div class="p-3.5 border-t">
        <p class="text-sm">{{props.contents}}</p>
      </div>
    </div>
  </transition>
</template>
