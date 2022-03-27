<script lang="ts" setup>
import { onMounted, reactive, ref, watchEffect } from 'vue'
import { IControl } from './types'
import { nanoid } from 'nanoid'

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
  wrapperInfo = wrapper.value!.getBoundingClientRect()
})

window.addEventListener('resize', () => {
  wrapperInfo = wrapper.value!.getBoundingClientRect()
})

const handleMouseDown = (e: MouseEvent): void => {
  currentNode = (controls.find((c) => c.attrs.id === (e.currentTarget as HTMLElement).getAttribute('id')) as IControl)

  const el: HTMLElement = (e.currentTarget as HTMLElement)
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

  const el: HTMLElement = (e.currentTarget as HTMLElement)

  const moveX = e.clientX
  const moveY = e.clientY

  elX = moveX - distanceLeft - wrapperInfo.x
  elY = moveY - distanceTop - wrapperInfo.y

  // debugger

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

  if (moveY + distanceBottom > wrapperInfo.height + wrapperInfo.top) {
    elY = wrapperInfo.height - elHeight
  }
  // 下边界

  setCoordinate(elX, elY)
}
const stopCtrl = (): void => {
  if (!inDrag) return
  inDrag = false
}

const setCoordinate = (x: number, y: number): void => {
  if (currentNode.type === 'text') {
    y += document.getElementById(currentNode.attrs.id as string)!.getBoundingClientRect().height
  }
  currentNode.attrs['transform'] = `translate(${x}, ${y})`
}

const wrapperSize: { width: number, height: number } = reactive({
  width: 600,
  height: 400,
})

const wrapper = ref<HTMLInputElement | null>(null)
let currentNode: IControl

const controls = reactive<IControl[]>([
  {
    type: 'rect',
    attrs: {
      x: 0,
      y: 0,
      width: 100,
      height: 100,
      fill: '#000c25',
      id: nanoid(),
    },
  },
  {
    type: 'rect',
    attrs: {
      x: 0,
      y: 0,
      width: 100,
      height: 100,
      transform: 'translate(100, 100)',
      fill: '#000c25',
      id: nanoid(),
    },
  },
  {
    type: 'text',
    attrs: {
      id: nanoid(),
      x: 0,
      y: 0,
      transform: 'translate(300, 200)',
      'font-size': 32,
      'font-weight': 500,
      text: 'zhu-hong',
      fill: '#FFFFFF',
    },
  },
])

watchEffect(() => {
  controls.forEach((c) => {
    let node = document.getElementById(c.attrs.id as string)
    if(node) {
      Object.keys(c.attrs).forEach((k) => {
        if(k === 'text') {
          node!.textContent = c.attrs[k] as string
        }
        node?.setAttribute(k, c.attrs[k] as string)
      })
    } else {
      const eln = document.createElementNS('http://www.w3.org/2000/svg', c.type)
      Object.keys(c.attrs).forEach((k) => {
        if(k === 'text') {
          eln!.textContent = c.attrs[k] as string
        }
        eln.setAttribute(k, c.attrs[k] as string)
      })
  
      eln.addEventListener('mousedown', handleMouseDown)
      eln.addEventListener('mousemove', handleMouseMove)
  
      wrapper.value?.append(eln)
    }

  })
})
</script>

<template>
  <main
    bg="[#050505]"
    w="screen"
    h="screen"
    flex="~"
    justify="between"
    overflow="auto"
  >
    <div w="260px" border="r gray-900" flex="~ col none"></div>
    <div flex="1 ~ none" justify="center" align="items-center" w="min-800px">
      <div :style="{width: `${wrapperSize.width}px`, height: `${wrapperSize.height}px`,}" shadow="~ dark-900 lg">
        <svg
          width="100%"
          height="100%"
          version="1.1"
          xmlns="http://www.w3.org/2000/svg"
          xmlns:xlink="http://www.w3.org/1999/xlink"
          @mouseup="stopCtrl"
          @mouseout="stopCtrl"
          ref="wrapper"
        >
        </svg>
      </div>
    </div>
  </main>
</template>

<style>
* {
  user-select: none;
}
</style>
