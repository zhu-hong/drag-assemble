<script setup lang="ts">
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
const windowWidth = window.innerWidth
const windowHeight = window.innerHeight

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

  elX = moveX - distanceLeft
  elY = moveY - distanceTop

  if (elX <= 0) {
    elX = 0
  }
  // 左边界

  if (elY <= 0) {
    elY = 0
  }
  // 上边界

  if (moveX + distanceRight > windowWidth) {
    elX = windowWidth - elWidth
  }
  // 右边界

  if (moveY + distanceBottom > windowHeight) {
    elY = windowHeight - elHeight
  }
  // 下边界

  setCoordinate(elX, elY, el)
}
const handleMouseUp = (e: MouseEvent): void => {
  inDrag = false
}

const setCoordinate = (x: number, y: number, el: HTMLElement): void => {
  el.style.top = `${y}px`
  el.style.left = `${x}px`
}
</script>

<template>
  <main bg="[#050505]" w="screen" h="screen" overflow="hidden" relative="~" flex="~" justify="between">
    <div
      v-for="e of 5"
      absolute="~"
      w="200px"
      h="200px"
      bg="dark-400"
      hover="bg-dark-600"
      active="bg-dark-800 cursor-move"
      transition="colors"
      @mousedown="handleMouseDown"
      @mousemove="handleMouseMove"
      @mouseup="handleMouseUp"
      @mouseout="handleMouseUp"
    ></div>
  </main>
</template>
