<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import * as Tone from 'tone'


const WIDTH = 2000
const HEIGHT = 1000

const panel = ref<HTMLDListElement | null>(null)
const canvas = ref<HTMLCanvasElement | null>(null)
const context = ref<CanvasRenderingContext2D | null>(null)

let time = 0

const synth = new Tone.Synth().toDestination();
const now = Tone.now();

const buttons = {
    btyellow: {
        state: 'inactive',
        colorInactive: 'yellow',
        colorActive: 'white',
        colorPlaying: 'orange'
    },

    btred: {
        state: 'inactive',
        colorInactive: 'red',
        colorActive: 'white',
        colorPlaying: 'pink'
    },

    btgreen: {
        state: 'inactive',
        colorInactive: 'green',
        colorActive: 'white',
        colorPlaying: 'lightgreen'
    },

    btblue: {
        state: 'inactive',
        colorInactive: 'blue',
        colorActive: 'white',
        colorPlaying: 'lightblue'
    },

}

function handleKeyDown(event) {
    if (event.key === "q") {
        buttons.btyellow.state = 'active'
    }
    if (event.key === "z") {
        buttons.btred.state = 'active'
    }
    if (event.key === "a") {
        buttons.btgreen.state = 'active'
    }
    if (event.key === "s") {
        buttons.btblue.state = 'active'
    }
}

function handleKeyUp(event) {
    if (event.key === "q") {
        buttons.btyellow.state = "playing"
        synth.triggerAttackRelease("C4", now);
        setTimeout(() => {
            buttons.btyellow.state = "inactive"
        }, 1000);
    }
    if (event.key === "z") {
        buttons.btred.state = "playing"
        synth.triggerAttackRelease("E4", now);
        setTimeout(() => {
            buttons.btred.state = "inactive"
        }, 1000);
    }
    if (event.key === "a") {
        buttons.btgreen.state = "playing"
        synth.triggerAttackRelease("G4", now);
        setTimeout(() => {
            buttons.btgreen.state = "inactive"
        }, 1000);
    }
    if (event.key === "s") {
        buttons.btblue.state = "playing"
        synth.triggerAttackRelease("A4", now);
        setTimeout(() => {
            buttons.btblue.state = "inactive"
        }, 1000);
    }
}

onMounted(() => {
    if (!canvas.value) return
    context.value = canvas.value!.getContext('2d')
    if (!context.value) return
    if (!panel.value) return

    window.addEventListener("keydown", handleKeyDown);
    window.addEventListener("keyup", handleKeyUp);
    requestAnimationFrame(render)
})

onUnmounted(() => {
    window.removeEventListener("keydown", handleKeyDown);
    window.removeEventListener("keyup", handleKeyUp);
    context.value = null
})

function render(timestamp: number) {
    if (!context.value) return

    const ctx = context.value
    ctx.clearRect(0, 0, WIDTH, HEIGHT)
    time = timestamp

    const circleYellow = new Path2D()
    circleYellow.arc(400, 450, 40, 0, 2 * Math.PI)

    if (buttons.btyellow.state === 'active') {
        ctx.fillStyle = buttons.btyellow.colorActive
    }
    if (buttons.btyellow.state === 'inactive') {
        ctx.fillStyle = buttons.btyellow.colorInactive
    }
    if (buttons.btyellow.state === 'playing') {
        ctx.fillStyle = buttons.btyellow.colorPlaying
    }
    ctx.fill(circleYellow)

    const circleRed = new Path2D()
    circleRed.arc(500, 350, 40, 0, 2 * Math.PI)

    if (buttons.btred.state === 'active') {
        ctx.fillStyle = buttons.btred.colorActive
    }
    if (buttons.btred.state === 'inactive') {
        ctx.fillStyle = buttons.btred.colorInactive
    }
    if (buttons.btred.state === 'playing') {
        ctx.fillStyle = buttons.btred.colorPlaying
    }

    ctx.fill(circleRed)

    const circleGreen = new Path2D()
    circleGreen.arc(400, 350, 40, 0, 2 * Math.PI)

    if (buttons.btgreen.state === 'active') {
        ctx.fillStyle = buttons.btgreen.colorActive
    }
    if (buttons.btgreen.state === 'inactive') {
        ctx.fillStyle = buttons.btgreen.colorInactive
    }
    if (buttons.btgreen.state === 'playing') {
        ctx.fillStyle = buttons.btgreen.colorPlaying
    }
    ctx.fill(circleGreen)

    const circleBlue = new Path2D()
    circleBlue.arc(500, 450, 40, 0, 2 * Math.PI)

    if (buttons.btblue.state === 'active') {
        ctx.fillStyle = buttons.btblue.colorActive
    }
    if (buttons.btblue.state === 'inactive') {
        ctx.fillStyle = buttons.btblue.colorInactive
    }
    if (buttons.btblue.state === 'playing') {
        ctx.fillStyle = buttons.btblue.colorPlaying
    }

    ctx.fill(circleBlue)

    requestAnimationFrame(render)
}

</script>


<template>
    <div ref="panel"></div>
    <canvas :width="WIDTH" :height="HEIGHT" ref="canvas"></canvas>
</template>


<style></style>