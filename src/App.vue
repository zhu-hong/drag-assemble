<script setup lang="ts">
import { onMounted } from 'vue';

let [
  elWidth,
  elHeight,
  elX,
  elY,
  clientX,
  clientY,
  distanceTop,
  distanceLeft,
  distanceBottom,
  distanceRight,
]: number[] = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
// 被拖动元素的宽高/元素的坐标/鼠标的坐标/鼠标距元素上下左右的坐标

let inDrag: boolean = false
let wrapperInfo: DOMRect

onMounted(() => {
  wrapperInfo = document.querySelector('svg')!.getBoundingClientRect()
})

window.addEventListener('resize', () => {
  wrapperInfo = document.querySelector('svg')!.getBoundingClientRect()
})

const handleMouseDown = (e: MouseEvent): void => {
  const el: HTMLElement = (e.target as HTMLElement)
  const elInfo = el.getBoundingClientRect()
  elWidth = elInfo.width
  elHeight = elInfo.height

  elX = elInfo.left
  elY = elInfo.top

  clientX = e.clientX
  clientY = e.clientY

  distanceLeft = clientX - elX
  distanceTop = clientY - elY
  distanceBottom = elHeight - distanceTop
  distanceRight = elWidth - distanceLeft

  inDrag = true
}
const handleMouseMove = (e: MouseEvent): void => {
  if (!inDrag) return

  const el: HTMLElement = (e.target as HTMLElement)

  const moveX = e.clientX
  const moveY = e.clientY

  elX = moveX - distanceLeft - wrapperInfo.x
  elY = moveY - distanceTop -wrapperInfo.y

  if (elX <= 0) {
    elX = 0
  }
  // 左边界

  if (elY <= 0) {
    elY = 0
  }
  // 上边界

  if (moveX + distanceRight > wrapperInfo.width + wrapperInfo.left) {
    elX = wrapperInfo.width - elWidth
  }
  // 右边界

  if (moveY + distanceBottom > wrapperInfo.height + wrapperInfo.y) {
    elY = wrapperInfo.height - elHeight
  }
  // 下边界

  setCoordinate(elX, elY, el)
}
const handleMouseUp = (e: MouseEvent): void => {
  inDrag = false
}

const setCoordinate = (x: number, y: number, el: HTMLElement): void => {
  el.setAttribute('transform', `translate(${x},${y})`)
}
</script>

<template>
  <main
    bg="[#050505]"
    w="screen"
    h="screen"
    overflow="hidden"
    relative="~"
    flex="~"
    justify="center"
  >
    <div border="~ light-700" h="300px">
      <svg
        width="400"
        height="300"
        version="1.1"
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
        viewBox="0 0 400 300"
      >
        <rect
          x="0"
          y="0"
          width="100"
          height="100"
          fill="#456"
          @mousedown="handleMouseDown"
          @mousemove="handleMouseMove"
          @mouseup="handleMouseUp"
          @mouseout="handleMouseUp"
          active="cursor-move"
        />
        <text fill="#FFFFFF" font-size="16" x="100" y="100">
          <tspan>资产编号是彩色打印机</tspan>
        </text>
        <rect
          x="0"
          y="0"
          width="100"
          height="100"
          fill="#123"
          @mousedown="handleMouseDown"
          @mousemove="handleMouseMove"
          @mouseup="handleMouseUp"
          @mouseout="handleMouseUp"
          active="cursor-move"
        />
      </svg>
    </div>
  </main>
</template>
