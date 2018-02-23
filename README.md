[![NPM](https://nodei.co/npm/expo-three.png)](https://nodei.co/npm/expo-three/)

# expo-three

Инструмент для использования three.js в построении нативного 3D experiences 💙

[ARKit Example](https://snack.expo.io/@bacon/arkit-example)

### Установка

```bash
yarn add three expo-three
```

### Использование

Импортируйте библиотеку в ваш JavaScript файл:

```js
import ExpoTHREE, { THREE } from 'expo-three';
```

## Функции

### `ExpoTHREE.renderer({ gl: WebGLRenderingContext, ...extras })`

Given a `gl` from an
[`Expo.GLView`](https://docs.expo.io/versions/latest/sdk/gl-view.html), возвращает
[`THREE.WebGLRenderer`](https://threejs.org/docs/#api/renderers/WebGLRenderer)
которое в нем отрисовано.

#### Возвраты

| Свойства |                                      Тип                                      | Описание                                              |
| -------- | :----------------------------------------------------------------------------: | -------------------------------------------------------- |
| renderer | [`THREE.WebGLRenderer`](https://threejs.org/docs/#api/renderers/WebGLRenderer) | Three.js рендер используемый при трисовке в GL Context |

#### Пример

```js
const renderer = ExpoTHREE.renderer({
  gl,
});
```

---

### `ExpoTHREE.loadAsync()`

Функция, которая будет асинхронно загружать файлы на основе их расширения.

#### Props

**Image Format**

* `number`: Статическая ссылка на файл `require('./model.*')`
* `Array<number>`: Коллекция статических ссылок на файлы
  `[require('./model.*')]`
* `string`: The Expo.Asset
  [`localUri`](https://docs.expo.io/versions/latest/sdk/asset.html#localuri)
* `Array<string>`: Collection of Expo.Asset
  [`localUri`](https://docs.expo.io/versions/latest/sdk/asset.html#localuri)s
* `Expo.Asset`
* `Array<Expo.Asset>`

```js
type ImageFormat = {
  uri: string,
};
export type WildCard = Expo.Asset | number | string | ImageFormat;
```

| Свойство      |           Тип             | Описание                                                         |
| ------------- | :-----------------------: | ---------------------------------------------------------------- |
| resource      |         WildCard          | Актив, который будет анализироваться асинхронно                  |
| onProgress    |       (xhr) => void       | Функция, вызываемая с событием xhr                               |
| assetProvider | () => Promise<Expo.Asset> | Функция, которая вызывается всякий раз, когда запрашивается неизвестный объект |

#### Returns

Это возвращает много разных вещей, основанных на входном файле 😅

#### Example

Список поддерживаемых форматов [здесь](/examples/loader)

```js
const texture = await ExpoTHREE.loadAsync('https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_272x92dp.png');
```

---

### `ExpoTHREE.createARCamera()`

Given an `arSession` from `NativeModules.ExponentGLViewManager.startARSession`,
return a
[`THREE.PerspectiveCamera`](https://threejs.org/docs/#api/cameras/PerspectiveCamera)
that automatically updates its view and projection matrices to reflect the AR
session camera. `width, height` specify the dimensions of the target viewport to
render to and `near, far` specify the near and far clipping distances
respectively. The `THREE.PerspectiveCamera` returned has its `updateMatrixWorld`
and `updateProjectionMatrix` methods overriden to update to the AR session's
state automatically.

#### Props

| Property  |  Type  | Description                                                           |
| --------- | :----: | --------------------------------------------------------------------- |
| arSession | number | ID returned from `NativeModules.ExponentGLViewManager.startARSession` |
| width     | number | Width of the gl context                                               |
| height    | number | Height of the gl context                                              |
| near      | number | The near clipping plane of the camera                                 |
| far       | number | The far clipping plane of the camera                                  |

#### Returns

| Property |                                         Type                                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                      |
| -------- | :----------------------------------------------------------------------------------: | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| camera   | [`THREE.PerspectiveCamera`](https://threejs.org/docs/#api/cameras/PerspectiveCamera) | A camera that automatically updates its view and projection matrices to reflect the AR session camera. `width, height` specify the dimensions of the target viewport to render to and `near, far` specify the near and far clipping distances respectively. The `THREE.PerspectiveCamera` returned has its `updateMatrixWorld` and `updateProjectionMatrix` methods overriden to update to the AR session's state automatically. |

#### Example

```js
const camera = ExpoTHREE.createARCamera(
  arSession,
  gl.drawingBufferWidth,
  gl.drawingBufferHeight,
  0.01,
  1000,
);
```

---

### `ExpoTHREE.createARBackgroundTexture()`

Returns a [`THREE.Texture`](https://threejs.org/docs/#api/textures/Texture) that
reflects the live video feed of the AR session. Usually this is set as the
`.background` property of a
[`THREE.Scene`](https://threejs.org/docs/#api/scenes/Scene) to render the video
feed behind the scene's objects.

#### Props

| Property  |                                      Type                                      | Description                                                           |
| --------- | :----------------------------------------------------------------------------: | --------------------------------------------------------------------- |
| arSession |                                     number                                     | ID returned from `NativeModules.ExponentGLViewManager.startARSession` |
| renderer  | [`THREE.WebGLRenderer`](https://threejs.org/docs/#api/renderers/WebGLRenderer) | The current renderer used with the GL context                         |

#### Returns

| Property |                               Type                                | Description                                                                                                    |
| -------- | :---------------------------------------------------------------: | -------------------------------------------------------------------------------------------------------------- |
| texture  | [`THREE.Texture`](https://threejs.org/docs/#api/textures/Texture) | A GL Texture of the camera stream for use with the [`THREE.Scene`](https://threejs.org/docs/#api/scenes/Scene) |

#### Example

```js
scene.background = ExpoTHREE.createARBackgroundTexture(arSession, renderer);
```

---

### `ExpoTHREE.getARLightEstimation()`

#### Props

| Property  |  Type  | Description                                                           |
| --------- | :----: | --------------------------------------------------------------------- |
| arSession | number | ID returned from `NativeModules.ExponentGLViewManager.startARSession` |

#### Returns

| Property                                                                                                                   |  Type  | Description                                                                   |
| -------------------------------------------------------------------------------------------------------------------------- | :----: | ----------------------------------------------------------------------------- |
| [ambientIntensity](https://developer.apple.com/documentation/arkit/arlightestimate/2878308-ambientintensity)               | number | This value ranges from 0 - 2000. 0 being very dark and 2000 being very bright |
| [ambientColorTemperature](https://developer.apple.com/documentation/arkit/arlightestimate/2921629-ambientcolortemperature) | number | This value ranges from 0 - 6500. This value is in kelvins and 6500 is white   |

#### Example

```js
const {
  ambientIntensity,
  ambientColorTemperature,
} = ExpoTHREE.getARLightEstimation(arSession);
```

---

### `ExpoTHREE.getRawFeaturePoints()`

#### Props

| Property  |  Type  | Description                                                           |
| --------- | :----: | --------------------------------------------------------------------- |
| arSession | number | ID returned from `NativeModules.ExponentGLViewManager.startARSession` |

#### Returns

```js
type Anchor = {
  x: number,
  y: number,
  z: number,
  id: number,
};
```

| Property        |     Type      | Description                                  |
| --------------- | :-----------: | -------------------------------------------- |
| featurePoints   | Array<Anchor> | An array of anchor positions and identifiers |

#### Example

```js
const { featurePoints } = ExpoTHREE.getRawFeaturePoints(arSession);
featurePoints.forEach(({ x, y, z, id }) => {});
```

---

### `ExpoTHREE.getPlanes()`

#### Props

| Property  |  Type  | Description                                                           |
| --------- | :----: | --------------------------------------------------------------------- |
| arSession | number | ID returned from `NativeModules.ExponentGLViewManager.startARSession` |

#### Returns

```js
type Point = {
  x: number,
  y: number,
  z: number,
};

type Extent = {
  width: number,
  height: number,
};

type Matrix = Array<number>;

type Plane = {
  center: Point,
  extent: Extent,
  transform: Matrix,
  id: number,
};
```

| Property |     Type     | Description                                   |
| -------- | :----------: | --------------------------------------------- |
| planes   | Array<Plane> | An array of horizontal planes and identifiers |

#### Example

```js
const { planes } = ExpoTHREE.getPlanes(arSession);
planes.forEach(
  ({ center: { x, y, z }, extent: { width, height }, transform, id }) => {},
);
```

---

### `ExpoTHREE.setIsLightEstimationEnabled()`

#### Props

| Property                 |  Type  | Description                                                           |
| ------------------------ | :----: | --------------------------------------------------------------------- |
| arSession                | number | ID returned from `NativeModules.ExponentGLViewManager.startARSession` |
| isLightEstimationEnabled |  bool  | Enable or disable light estimation                                    |

#### Example

```js
ExpoTHREE.setIsLightEstimationEnabled(arSession, true);
```

---

### `ExpoTHREE.setIsPlaneDetectionEnabled()`

#### Props

| Property                |  Type  | Description                                                           |
| ----------------------- | :----: | --------------------------------------------------------------------- |
| arSession               | number | ID returned from `NativeModules.ExponentGLViewManager.startARSession` |
| isPlaneDetectionEnabled |  bool  | Enable or disable plane detection                                     |

#### Example

```js
ExpoTHREE.setIsPlaneDetectionEnabled(arSession, true);
```

---

### `ExpoTHREE.setWorldAlignment()`

#### Props

```js
ExpoTHREE.WorldAlignment = {
  Gravity: 0,
  GravityAndHeading: 1,
  Camera: 2,
};
```

| Property       |           Type           | Description                                                           |
| -------------- | :----------------------: | --------------------------------------------------------------------- |
| arSession      |          number          | ID returned from `NativeModules.ExponentGLViewManager.startARSession` |
| worldAlignment | ExpoTHREE.WorldAlignment | Set the world alignment                                               |

#### Example

```js
ExpoTHREE.setWorldAlignment(arSession, ExpoTHREE.WorldAlignment.Gravity);
```

---

## `ExpoTHREE.utils`

### `ExpoTHREE.utils.alignMesh()`

#### Props

```js
type Axis = {
  x?: number,
  y?: number,
  z?: number,
};
```

| Property |    Type     | Description                       |
| -------- | :---------: | --------------------------------- |
| mesh     | &THREE.Mesh | The mesh that will be manipulated |
| axis     |    ?Axis    | Set the relative center axis      |

#### Example

```js
ExpoTHREE.utils.alignMesh(mesh, { x: 0.0, y: 0.5 });
```

---

### `ExpoTHREE.utils.scaleLongestSideToSize()`

#### Props

| Property |    Type     | Description                                                  |
| -------- | :---------: | ------------------------------------------------------------ |
| mesh     | &THREE.Mesh | The mesh that will be manipulated                            |
| size     |   number    | The size that the longest side of the mesh will be scaled to |

#### Example

```js
ExpoTHREE.utils.scaleLongestSideToSize(mesh, 3.2);
```

---

### `ExpoTHREE.utils.computeMeshNormals()`

Used for smoothing imported geometry, specifically when imported from `.obj` models.

#### Props

| Property |    Type     | Description                       |
| -------- | :---------: | --------------------------------- |
| mesh     | &THREE.Mesh | The mesh that will be manipulated |

#### Example

````js
ExpoTHREE.utils.computeMeshNormals(mesh);
```

---

## THREE Extensions

### `suppressExpoWarnings`

A function that suppresses EXGL compatibility warnings and logs them instead.
You will need to import the `ExpoTHREE.THREE` global instance to use this. By
default this function will be activated on import.

* `shouldSuppress`: boolean

```js
import { THREE } from 'expo-three';
THREE.suppressExpoWarnings(true);
````

### [`loadCubeTextureAsync`](https://snack.expo.io/@bacon/expo-three-loadcubetextureasync)

Used to load in a texture cube or skybox.

* `assetForDirection`: This function will be called for each of the 6
  directions.
  * `({ direction })`: A direction string will be passed back looking for the
    corresponding image. You can send back: `static resource`, `localUri`,
    `Expo.Asset`, `remote image url`
* `directions`: The order that image will be requested in. The default value is:
  `['px', 'nx', 'py', 'ny', 'pz', 'nz']`

Example:

```js
const skybox = {
	nx: require('./nx.jpg'),
	ny: require('./ny.jpg'),
	nz: require('./nz.jpg'),
	px: require('./px.jpg'),
	py: require('./py.jpg'),
	pz: require('./pz.jpg')
}
scene.background = await loadCubeTextureAsync({
  assetForDirection: ({ direction }) => skybox[direction],
})
```

---

## Example

This is based on
[https://threejs.org/docs/#manual/introduction/Creating-a-scene](https://threejs.org/docs/#manual/introduction/Creating-a-scene).

```js
import Expo from 'expo';
import React from 'react';
import * as THREE from 'three';
import ExpoTHREE from 'expo-three'; // 2.2.2-alpha.1

export default class App extends React.Component {
  render() {
    // Create an `Expo.GLView` covering the whole screen, tell it to call our
    // `_onGLContextCreate` function once it's initialized.
    return (
      <Expo.GLView
        style={{ flex: 1 }}
        onContextCreate={this._onGLContextCreate}
      />
    );
  }

  // This is called by the `Expo.GLView` once it's initialized
  _onGLContextCreate = async gl => {
    // Based on https://threejs.org/docs/#manual/introduction/Creating-a-scene
    // In this case we instead use a texture for the material (because textures
    // are cool!). All differences from the normal THREE.js example are
    // indicated with a `NOTE:` comment.

    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      75,
      gl.drawingBufferWidth / gl.drawingBufferHeight,
      0.1,
      1000
    );

    // NOTE: How to create an `Expo.GLView`-compatible THREE renderer
    const renderer = ExpoTHREE.createRenderer({ gl });
    renderer.setSize(gl.drawingBufferWidth, gl.drawingBufferHeight);

    const geometry = new THREE.BoxGeometry(1, 1, 1);
    //  NOTE: How to create an Expo-compatible THREE texture
    const texture = await ExpoTHREE.loadAsync(
      require('./assets/icons/app-icon.png')
    );
    const material = new THREE.MeshBasicMaterial({ map: texture });
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);

    camera.position.z = 5;

    const render = () => {
      requestAnimationFrame(render);

      cube.rotation.x += 0.07;
      cube.rotation.y += 0.04;

      renderer.render(scene, camera);

      // NOTE: At the end of each frame, notify `Expo.GLView` with the below
      gl.endFrameEXP();
    };
    render();
  };
}

```

## Links

* [Loading Text](https://github.com/EvanBacon/expo-three-text)
* [ARKit Tutorial](https://blog.expo.io/introducing-expo-ar-mobile-augmented-reality-with-javascript-powered-by-arkit-b0d5a02ff23)
* [ARKit Example](https://snack.expo.io/@bacon/arkit-example)

* [three.js docs](https://threejs.org/docs/)

* [Random Demos](https://github.com/EvanBacon/expo-three-demo)
* [Game: Expo Sunset Cyberspace](https://github.com/EvanBacon/Sunset-Cyberspace)
* [Game: Crossy Road](https://github.com/EvanBacon/Expo-Crossy-Road)
* [Game: Nitro Roll](https://github.com/EvanBacon/Expo-Nitro-Roll)
* [Game: Pillar Valley](https://github.com/EvanBacon/Expo-Pillar-Valley)
* [Voxel Terrain](https://github.com/EvanBacon/Expo-Voxel)
