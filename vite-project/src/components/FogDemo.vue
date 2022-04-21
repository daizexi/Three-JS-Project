<template>
  <div ref='el'></div>
</template>

<script>
import { defineComponent } from '@vue/composition-api'
import { onMounted, ref } from '@vue/runtime-core'
import * as THREE from 'three'
import { TrackballControls } from 'three/examples/jsm/controls/TrackballControls.js'
import * as dat from 'dat.gui'

export default defineComponent({
    setup () {
        const el = ref(null)
        onMounted(() => {
            const scene = new THREE.Scene()
            const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000)
            camera.position.set(10, 10, 10)
            camera.lookAt(scene.position)

            const renderer = new THREE.WebGLRenderer()
            renderer.setClearColor(new THREE.Color(0x000000)) // 设置渲染场景时的背景颜色
            renderer.setSize(window.innerWidth, window.innerHeight)

            const planeGeo = new THREE.PlaneGeometry(10, 15)
            const basicMaterial = new THREE.MeshLambertMaterial()
            const plane = new THREE.Mesh(planeGeo, basicMaterial)
            plane.position.set(0, 0, 0)
            plane.rotation.x = -0.5 * Math.PI
            scene.add(plane)

            const axesHelper = new THREE.AxesHelper(5)
            scene.add(axesHelper)

            const cubeGeo = new THREE.BoxGeometry(1, 1, 1)
            const cubeMaterial = new THREE.MeshLambertMaterial({ color: 0x7777FF })
            const cube = new THREE.Mesh(cubeGeo, cubeMaterial)
            // cube.castShadow = true
            cube.position.set(-2, 2, 2)
            scene.add(cube)

            // const color = 0xffffff
            // const near = 1
            // const far = 10
            // scene.fog = new THREE.Fog(color, near, far)

            let spotLight = new THREE.SpotLight('#ffffff')
            spotLight.position.set(5, 7, -5)
            spotLight.castShadow = true
            spotLight.shadow.camera.near = 1
            spotLight.shadow.camera.far = 100
            spotLight.target = plane
            spotLight.distance = 0
            spotLight.angle = 0.4
            spotLight.shadow.camera.fov = 120
            spotLight.intensity = 2
            scene.add(spotLight)

            let pp = new THREE.SpotLightHelper(spotLight)
            scene.add(pp)

            el.value.appendChild(renderer.domElement)
            renderer.render(scene, camera)
            renderer.shadowMap.Enabled = true

            const trackBallControls = new TrackballControls(camera, renderer.domElement)
            trackBallControls.rotateSpeed = 1.0
            trackBallControls.zoomSpeed = 1.0
            trackBallControls.panSpeed = 1.0

            const gui = new dat.GUI()
            const controls = new function () {
                this.cubeX = 1
                this.cubeY = 1
                this.cubeZ = 1
                this.lightPosX = 5
                this.lightPosY = 7
                this.lightPosZ = -5
            }()
            gui.add(controls, 'cubeX', 1, 10).onChange(() => {
                cube.scale.x = controls.cubeX
            })
            gui.add(controls, 'cubeY', 1, 10).onChange(() => {
                cube.scale.y = controls.cubeY
            })
            gui.add(controls, 'cubeZ', 1, 10).onChange(() => {
                cube.scale.z = controls.cubeZ
            })
            gui.add(controls, 'lightPosX', -10, 10)
            gui.add(controls, 'lightPosY', -10, 10)
            gui.add(controls, 'lightPosZ', -10, 10)

            function animate () {
                trackBallControls.update()
                // cube.scale.set(controls.cubeX, controls.cubeY, controls.cubeZ)
                spotLight.position.set(controls.lightPosX, controls.lightPosY, controls.lightPosZ)
                spotLight.lookAt(scene.position)
                let pp = new THREE.SpotLightHelper(spotLight)
                scene.add(pp)
                requestAnimationFrame(animate)
                renderer.render(scene, camera)
            }
            animate()
        })
        return { el }
    }

})
</script>

<style>

</style>
