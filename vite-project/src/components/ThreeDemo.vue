<template>
    <div id="webgl-output" ref="el"></div>
</template>

<script>
import { defineComponent } from '@vue/composition-api'
import { onMounted } from '@vue/runtime-core'
import * as THREE from 'three'

export default defineComponent({
    setup () {
        // const el = ref(null)
        onMounted(() => {
            const scene = new THREE.Scene()

            const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000)
            camera.position.set(-30, 40, 30)
            camera.lookAt(scene.position)

            const renderer = new THREE.WebGLRenderer()
            renderer.setClearColor(new THREE.Color(0x000000)) // 设置渲染场景时的背景颜色
            renderer.setSize(window.innerWidth, window.innerHeight)

            const planeGeo = new THREE.PlaneGeometry(100, 100)
            const material = new THREE.MeshBasicMaterial({ color: 0xffffff }) // 没有光源，只能使用basic材质，同时basic材质也不会对光源有反应
            const plane = new THREE.Mesh(planeGeo, material)
            plane.rotation.x = -0.5 * Math.PI
            plane.position.set(15, 0, 0)
            scene.add(plane)

            // create a cube
            let cubeGeometry = new THREE.BoxGeometry(4, 4, 4)
            let cubeMaterial = new THREE.MeshBasicMaterial({
                color: 0xFF0000,
                wireframe: true // 表示展示出构建几何体的三角形
            })
            let cube = new THREE.Mesh(cubeGeometry, cubeMaterial)

            // position the cube
            cube.position.set(-4, 3, 0)

            // add the cube to the scene
            scene.add(cube)

            // create a sphere
            let sphereGeometry = new THREE.SphereGeometry(4, 20, 20)
            let sphereMaterial = new THREE.MeshBasicMaterial({
                color: 0x7777FF,
                wireframe: true
            })
            let sphere = new THREE.Mesh(sphereGeometry, sphereMaterial)

            // position the sphere
            sphere.position.set(20, 4, 2)

            // add the sphere to the scene
            scene.add(sphere)

            document.getElementById('webgl-output').appendChild(renderer.domElement)

            renderer.render(scene, camera)
        })
        return {
        }
    }
})
</script>

<style lang="scss" scoped>

</style>
