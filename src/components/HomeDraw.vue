<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
type Point = { x: number; y: number }

const WIDTH = 2000
const HEIGHT = 1000
const EARTH_DIAMETER = 17.7
const SUN_DIAMETER = 25
const MOON_DIAMETER = 3.5
const EARTH_MOON_DISTANCE = 384.4
const FACTOR = 2

const panel = ref<HTMLDListElement | null>(null)
const canvas = ref<HTMLCanvasElement | null>(null)
const context = ref<CanvasRenderingContext2D | null>(null)
const mouse = { x: 0, y: 0 }


onMounted(() => {
    if (!canvas.value) return
    context.value = canvas.value!.getContext('2d')
    if (!context.value) return
    if (!panel.value) return

    context.value.font = '10px monospace'
    requestAnimationFrame(render)
})

onUnmounted(() => {
    context.value = null
})


function getPointAtAngle(
    center: Point,
    angle: number,
    distance: number
): Point {
    return {
        x: center.x + Math.cos(angle) * distance,
        y: center.y + Math.sin(angle) * distance,
    }
}

function move(event: MouseEvent) {
    mouse.x = event.offsetX
    mouse.y = event.offsetY
    // console.log(event)
}

function getAngle(a: Point, b: Point): number {
    return Math.atan2(b.y - a.y, b.x - a.x)
}

function render(timestamp: number) {
    if (!context.value) return

    const ctx = context.value
    ctx.clearRect(0, 0, WIDTH, HEIGHT)

    const sun = new Path2D()
    const sunPos = {
        x: 500,
        y: 350
    }
    // const sunPos = getAngle()
    sun.arc(sunPos.x, sunPos.y, SUN_DIAMETER * FACTOR, 0, 2 * Math.PI, true)

    const earth = new Path2D()
    const earthPos = getPointAtAngle(sunPos, getAngle(sunPos, mouse), 200)
    // const earthPos = getPointAtAngle(mouse, timestamp / 1500, 200)
    earth.arc(earthPos.x, earthPos.y, EARTH_DIAMETER * FACTOR, 0, 2 * Math.PI, true)

    const moon = new Path2D()
    // moon.arc(timestamp / 10, 200, MOON_DIAMETER * FACTOR, 0, 2 * Math.PI, true)

    const moonPos = getPointAtAngle(earthPos, timestamp / 500, 70)
    moon.arc(moonPos.x, moonPos.y, MOON_DIAMETER * FACTOR, 0, 2 * Math.PI, true)

    ctx.fillStyle = 'yellow'
    ctx.fill(sun)
    ctx.fillStyle = 'blue'
    ctx.fill(earth)
    ctx.fillStyle = 'white'
    ctx.fill(moon)

    // ctx.fillText(mouse.x, 100, 50)
    // ctx.fillText(mouse.y, 100, 60)

    requestAnimationFrame(render)
}



</script>

<template>
    <div ref="panel"></div>
    <canvas :width="WIDTH" :height="HEIGHT" ref="canvas" @mousemove="move"></canvas>
</template>

<style scoped></style>