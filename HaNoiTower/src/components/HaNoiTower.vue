<template>
  <div class="h1">
    <h1>Hà Nội Tower</h1>
  </div>
  <div class="container">
    <div
      class="block"
      v-for="(block, blockIndex) in blocks"
      :key="blockIndex"
      @dragover.prevent
      @drop="handleDrop(blockIndex)"
    >
      <div class="column"></div>
      <div
        v-for="(disk, index) in block"
        :key="index"
        :class="disk.class"
        :draggable="index === block.length - 1"
        @dragstart="
          index === block.length - 1 ? handleDragStart(blockIndex, index) : null
        "
      ></div>
    </div>
    <div class="footer"></div>
  </div>

  <div class="log">
    <a :class="styleLog">{{ log }}</a>
  </div>
</template>

<script setup>
import { reactive, ref } from "vue";
const log = ref("Chúc bạn may mắn!");
const styleLog = ref("");
const handleLog = (type) => {
  if (type === "error") {
    log.value = "Không thể xếp đĩa lớn hơn lên trên đĩa nhỏ hơn!";
    styleLog.value = "log-error";
  }
  if (type === "done") {
    log.value = "🎉 Chúc mừng! Bạn đã hoàn thành trò chơi!";
    styleLog.value = "log-done";
  }
};
const blocks = reactive([
  [
    { class: "line1", size: 3 },
    { class: "line2", size: 2 },
    { class: "line3", size: 1 },
  ],
  [],
  [],
]);
const draggedDisk = ref(null); //lưu thông tin đĩa

// xử lý khi bắt đầu kéo thả 1 đĩa
const handleDragStart = (blockIndex, diskIndex) => {
  draggedDisk.value = { blockIndex, diskIndex };
};

// xử lý khi đĩa được thả vào cột mới
const handleDrop = (targetBlockIndex) => {
  if (
    draggedDisk.value === null ||
    draggedDisk.value.blockIndex === targetBlockIndex
  )
    return;
  const { blockIndex, diskIndex } = draggedDisk.value;
  //đảm bảo chỉ có thể di chuyển đĩa nhỏ hơn nằm trên đĩa lớn hơn
  const disk = blocks[blockIndex].at(diskIndex);
  const targetBlock = blocks[targetBlockIndex];

  if (
    targetBlock.length === 0 ||
    disk.size < targetBlock[targetBlock.length - 1].size
  ) {
    blocks[blockIndex].splice(diskIndex, 1); //xóa đĩa khỏi cột hiện tại
    targetBlock.push(disk);
    log.value = "Cố lên xắp xong rồi!";
    styleLog.value = "log-wait";
    if(targetBlock.length === 3) handleLog("done")
  } else {
    handleLog("error");
  }
  draggedDisk.value = null; //reset
};
</script>
<style scoped>
.container {
  width: 1000px;
  height: 350px;
  margin: 0 auto;
  display: flex;
  justify-content: space-around;
  align-items: flex-end;
  position: relative;
}
.block {
  width: 30%;
  height: 100%;
  display: flex;
  flex-direction: column-reverse;
  align-items: center;
  position: relative;
}
.column {
  width: 30px;
  height: 300px;
  background-color: rgb(115, 120, 118);
  border-top-left-radius: 15px;
  border-top-right-radius: 15px;
  z-index: -1;
  position: absolute;
}
.footer {
  width: 100%;
  height: 50px;
  background-color: rgb(115, 120, 118);
  position: absolute;
  bottom: -14%;
  left: 0;
  right: 0;
  margin: 0 auto;
  border-radius: 10px;
  z-index: -1;
}
.line1,
.line2,
.line3 {
  height: 50px;
  border: 1px solid black;
  border-radius: 10px;
  cursor: pointer;
}
.line1 {
  width: 300px;
  background-color: rgb(253, 148, 50);
}
.line2 {
  width: 200px;
  background-color: rgb(19, 133, 255);
}
.line3 {
  width: 100px;
  background-color: rgb(242, 255, 2);
}
.h1 {
  background-color: rgb(5, 209, 255);
  border-radius: 15px;
  padding: 2px 15px;
  width: 200px;
  color: white;
  margin: 100px auto;
  text-align: center;
}
.log {
  margin: 100px auto;
  text-align: center;
  width: 500px;
  height: 100px;
  font-size: 30px;
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
  color: #2dee02;
}
.log-error {
  color: rgb(224, 20, 9) !important;
}
.log-done {
  color: gold !important;
}
.log-wait {
  color: rgba(20, 253, 187, 0.975) !important;
}
</style>
