# 360 video webVR Tutorial

This repo provides a three-step tutorial on how to use three.js to import your 360 video into a webpage and allow users to interact with it.

## Getting Started

Once you download the code, open your terminal and `cd` into this folder. You will need to start your local server to view the page correctly.

1.	Run `python SimpleHTTPServer` on your terminal
2.	Install http-server if you haven’t, then run `http-server`

### Prerequisites

I would learn a little bit javascript/HTML/CSS to start this journey.


## First Step

Let’s learn some basic principles of Three.js first. We’ll know how to create the basic scene after this chapter. You can open the init.html on your browser to see the result.

###Create a Three.js scene
Three.js is JavaScript library used to create and display animated 3D graphics in a browser. Three.js uses WebGL to render elements on <canvas>.

Creating a Three.js scene is similar to create a scene in 3D modeling soft wares like 3Ds Max. To render the scene with camera, you need at least three elements
1.	Scene. You will need to add an element to the scene for it to register as exist. To initiate a scene:

```
var scene =new THREE.Scene();
```

2.	Camera. It determines which part of the scene will be shown / hided. To initiate a camera:
```
var camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.z = 50;
```

3.	Renderer. It defines how it is shown in the browser. To initiate a renderer:
```
var renderer = new THREE.WebGLRenderer();// Setup three.js WebGL renderer.
renderer.setPixelRatio(window.devicePixelRatio);
renderer.setSize(window.innerWidth, window.innerHeight);
// Append the canvas element created by the renderer to #vr-container div element.
document.getElementById('vr-container').appendChild(renderer.domElement);
```

###Create the sphere

Creating an object needs to specify the geometry and the material. The object needs to be added to the scene to be visible.
```
var geometrySky = new THREE.SphereBufferGeometry( 5, 40, 20 );
//Create material of the sky. Here we give it a green color and display its wireframe.
var materialSky = new THREE.MeshBasicMaterial({ color: 0x00ff00, wireframe: true } );
meshSky = new THREE.Mesh( geometrySky, materialSky );
scene.add( meshSky );
```
###Add animation
Three.js uses requestAnimationFrame method tells the browser that you wish to perform an animation and requests the browser to update an animation before the next repaint.
```
animate();
function animate() {
    meshSky.rotation.y += 0.01; //rotate automatically
    // Render the scene.
    renderer.render(scene, camera);
    //Tell the browser that you wish to perform an animation and requests that the browser call a specified function to update an animation
    requestAnimationFrame(animate);
}
```
###Add resize function to make it responsive
```
window.addEventListener('resize', onResize, true);
function onResize() {
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
}
```
Now we have a rotating sphere!

## Built With

* [Three.js](https://threejs.org)
* [WebVR Polyfill](https://github.com/immersive-web/webvr-polyfill)

## Contributing
This project is greatly influenced by Three.js documentation (https://threejs.org/docs/index.html#examples/controls/OrbitControls) and 30 VR Projects In 30 Days(https://risonsimon.com/days-in-vr/).


## Authors

* **Yan Wu** - *Check out my portfolio website* - [hermionewy](https://hermionewy.github.io/data.html)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
