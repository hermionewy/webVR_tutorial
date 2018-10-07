# 360 video webVR Tutorial

This repo provides a three-step tutorial on how to use three.js to import your 360 video into a webpage and allow users to interact with it.

## Getting Started

Once you download the code, open your terminal and `cd` into this folder. You will need to start your local server to view the page correctly.

1.	Run `python SimpleHTTPServer` on your terminal
2.	Install http-server if you haven’t, then run `http-server`
3.  Check out [init.html](https://hermionewy.github.io/webVR_tutorial/init.html), [start.html](https://hermionewy.github.io/webVR_tutorial/start.html), [finish.html](https://hermionewy.github.io/webVR_tutorial/finish.html)

### Prerequisites

I would learn a little bit javascript/HTML/CSS to start this journey.


## Step 1:

Let’s learn some basic principles of Three.js first. We’ll know how to create the basic scene after this chapter. You can open the [init.html](https://hermionewy.github.io/webVR_tutorial/init.html) on your browser to see the result.

### Create a Three.js scene
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

### Create the sphere

Creating an object needs to specify the geometry and the material. The object needs to be added to the scene to be visible.
```
var geometrySky = new THREE.SphereBufferGeometry( 5, 40, 20 );
//Create material of the sky. Here we give it a green color and display its wireframe.
var materialSky = new THREE.MeshBasicMaterial({ color: 0x00ff00, wireframe: true } );
meshSky = new THREE.Mesh( geometrySky, materialSky );
scene.add( meshSky );
```
### Add animation
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
### Add resize function to make it responsive
```
window.addEventListener('resize', onResize, true);
function onResize() {
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
}
```
Now we have a rotating sphere!

## STEP 2:
Let’s change the texture of the sphere we created to the 360 video we shot and edited, change its size and adjust the distance of the camera to simulate the function of our eyes. You can open the start.html on your browser to see the result.

### Add video texture
To create the video texture, we will need to create a video element with Javascript.
```
var video = document.createElement( 'video' );
video.width = window.innerWidth; 
video.height = window.innerHeight; 
video.loop = true; 
video.muted = true; 
video.src = './maverick1.mp4'; 
video.crossOrigin = ''; 
video.setAttribute('webkit-playsinline', 'true'); 
video.setAttribute('playsinline', 'true'); 
video.load(); 
video.play(); //create video texture and add the video element to it. 
videoTexture = new THREE.VideoTexture( video ); 
//How the texture is sampled when a texel covers less than one pixel. 
videoTexture.minFilter = THREE.LinearFilter; 
//How the texture is sampled when a texel covers more than one pixel. 
videoTexture.magFilter = THREE.LinearFilter; 
videoTexture.format = THREE.RGBFormat;
```

In order to make things clearer, we restructure the code a little bit and use an onload() function to initiate the global variables.

###Add orbit control
We should have a rotating video in our browser now. It’s time to add an orbit controller to the camera to allow users to zoom and pan around the target of the camera.

After we include the [OrbitControl.js](https://threejs.org/docs/index.html#examples/controls/OrbitControls) into the script, we initiate a controller. It takes two parameters, the camera to be controlled and the DOM element used for event listeners.
```
var orbitController = new THREE.OrbitControls( camera, renderer.domElement); 
orbitController.update();
```
Now we have a 360 video rotating on our browser! However, if you zoom in/out too much, you will see the whole world become a ball. To disable zoom effect, use
```
orbitController.enableZoom=false;
```

## STEP 3:
Adding the VR controller is similar to adding the orbit controller, just a little bit more complicated. You can open the finish.html on your browser to see the result.

### Include related scripts
Firstly, we need to include a few Javascript files.

1. ```webvr-polyfill.js```, which ensures our WebVR content works on any platform, whether or not the browser/device has native WebVR support.

2.  ```VRControls.js``` which acquires positional information from connected VR devices and applies the transformations to a three.js camera object.
3.  ```VREffect.js```, which handles stereo camera setup and rendering.
4.  ```webvr-ui```, which provides a set of UI controls for entering VR mode.

### Add WebVR controller

Secondly, let’s add WebVR controller, which controls the camera. We adjust the camera position according to the positional data the vrController gets.
```
//create VR controls var vrController = new THREE.VRControls(camera); 
vrController.standing = true;
//set to user standing mode
camera.position.y = vrController.userHeight; // adjust camera position
```
Initiate WebVR icons to allow users to enter VR mode after click on the button.
```
// Initialize the WebVR UI. var
uiOptions = {     color: 'black',     background: 'white',     corners: 'square' };
 vrButton = new webvrui.EnterVRButton(renderer.domElement, uiOptions); 
vrButton.on('exit', function() { 
    camera.quaternion.set(0, 0, 0, 1); 
    camera.position.z = 300;
     });
 vrButton.on('hide', function() { 
    document.getElementById('ui').style.display = 'none'; 
    });
 vrButton.on('show', function() { 
    document.getElementById('ui').style.display = 'inherit';
     }); 
document.getElementById('vr-button').appendChild(vrButton.domElement); 
document.getElementById('magic-window').addEventListener('click', function() { 
    vrButton.requestEnterFullscreen(); 
    });  
document.getElementById('vr-button').addEventListener('click', function() { 
    video.play(); 
    });
```

Thirdly, we want to check the size of the VR display equipment and render our graphics based on the physics data we get.

In Javascript, the navigator.getVRDisplays() method returns a promise that resolves to an array of [VRDisplay](https://developer.mozilla.org/en-US/docs/Web/API/VRDisplay) objects representing any available VR displays connected to the computer. We use setupStage() function to get the Head-mounted display (HMD), if it specifies stageParameters, rearrange the scene accordingly.

```
function setupStage() {
     navigator.getVRDisplays() //learn more about navigator variable(https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getVRDisplays) 
        .then(function(displays) { 
                if (displays.length > 0) { 
                            vrDisplay = displays[0]; 
                            if (vrDisplay.stageParameters) { 
                                    setStageDimensions(vrDisplay.stageParameters); 
                            } 
                             vrDisplay.requestAnimationFrame(animate);
                                      } 
                             }); }

function setStageDimensions(stage) {
     // Make the skybox fit the stage. 
    var material = meshSky.material; 
    scene.remove(meshSky); 
    console.log(stage);     // Size the skybox according to the size of the actual stage.
     var geometry = new THREE.BoxGeometry(stage.sizeX, boxSize, stage.sizeZ);
    meshSky = new THREE.Mesh(geometry, material);      // Place it on the floor. 
    meshSky.position.y = boxSize/2; 
    scene.add(meshSky);
     }
```
If you publish your code to Github and view the finish.html on your phone, click “ENTER VR”, allow your phone to display horizontally, you should be able to see your mobile screen split into two views. If you put your phone in a cardboard, you should be able to enjoy your first web VR project.

[Why WebVR matters?](https://medium.com/immersion-for-the-win/why-webvr-matters-9f383fee04e5) It makes VR far more accessible. You don’t need to download a heavy app to your phone, you don’t need to wear a complicated and heavy helmet, and you don’t need to have certain kind of ceremony. All you need is to click a link. Besides, it attracts millions of web developers or even journalists to start building VR or 3D projects.

Bon appétit.

## Built With

* [Three.js](https://threejs.org)
* [WebVR Polyfill](https://github.com/immersive-web/webvr-polyfill)

## Contributing
This project is greatly influenced by [Three.js documentation] (https://threejs.org/docs/index.html#examples/controls/OrbitControls) and [30 VR Projects In 30 Days](https://risonsimon.com/days-in-vr/).


## Authors

* **Yan Wu** - *Check out my portfolio website* - [hermionewy](https://hermionewy.github.io/data.html)

## License

This project is licensed under the MIT License.
