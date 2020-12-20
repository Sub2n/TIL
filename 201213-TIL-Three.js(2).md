# Three.js Scene의 기본 구성요소

- Three.js scene에서 사용되는 구성요소들
- THREE.Scene 객체로 할 수 있는 것
- Geomtry와 Mesh의 연관성
- Orthographic (직교) 카메라와 Perspective (원근) 카메라의 차이

### Scene 작성

Scene을 표시하기 위해서는 아래와 같은 3가지 구성요소가 필요하다.

| 구성요소 | 설명                                                         |
| -------- | ------------------------------------------------------------ |
| Camera   | 화면에 어떤 것이 렌더링될 것인지 결정한다.                   |
| Lights   | 그림자 효과를 만들 때 물질(material)이 어떻게 보일 것인지 효과를 준다. |
| Objects  | 정육면체(cube), 구체(sphere) 등 카메라 관점에서 표현되는 메인 객체 |

THREE.Scene은 이런 여러 객체들의 컨테이너 역할을 수행한다. Scene 객체 자체는 많은 기능을 가지고 있지 않다.

> THREE.Scene은 종종 Scene graph라고도 불리는 구조체다. Scene graph는 그래픽 장면의 필요한 모든 정보를 담을 수 있는 구조체다. 이는 Three.js에서 THREE.Scene이 Object와 Light, 그리고 렌더링에 필요한 다른 모든 객체를 포함한다는 의미다. 주의해야할 것은 Scene graph는 객체의 단순 배열이 아니라 트리 구조 노드들의 집합이라는 점이다. Three.js의 Scene, 또는 THREE.Scene 자체에 추가할 수 있는 각각의 객체는 THREE.Object3D라는 객체에 기반해 확장된 것이다. THREE.Object3D 객체는 Three.js가 해석하고 렌더링할 객체 트리를 생성하는 데 사용되는 자식도 가질 수 있다.

### Scene의 기본 기능

```html
<!DOCTYPE html>

<html>

  <head>
    <title>Example 02.01 - Basic Scene</title>
    <script type="text/javascript" src="../libs/three.js"></script>

    <script type="text/javascript" src="../libs/stats.js"></script>
    <script type="text/javascript" src="../libs/dat.gui.js"></script>
    <style>
      body {
        /* set margin to 0 and overflow to hidden, to go fullscreen */
        margin: 0;
        overflow: hidden;
      }
    </style>
  </head>
  <body>

    <div id="Stats-output">
    </div>
    <!-- Div which will hold the Output -->
    <div id="WebGL-output">
    </div>

    <!-- Javascript code that runs our Three.js examples -->
    <script type="text/javascript">

      // once everything is loaded, we run our Three.js stuff.
      function init() {

        const stats = initStats();

        // create a scene, that will hold all our elements such as objects, cameras and lights.
        const scene = new THREE.Scene();

        // create a camera, which defines where we're looking at.
        const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
        scene.add(camera);

        // create a render and set the size
        const renderer = new THREE.WebGLRenderer();

        renderer.setClearColor(new THREE.Color(0xEEEEEE, 1.0));
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.shadowMapEnabled = true;

        // create the ground plane
        const planeGeometry = new THREE.PlaneGeometry(60, 40, 1, 1);
        const planeMaterial = new THREE.MeshLambertMaterial({color: 0xffffff});
        const plane = new THREE.Mesh(planeGeometry, planeMaterial);
        plane.receiveShadow = true;

        // rotate and position the plane
        plane.rotation.x = -0.5 * Math.PI;
        plane.position.set(0, 0, 0);

        // add the plane to the scene
        scene.add(plane);

        // position and point the camera to the center of the scene
        camera.position.set(-30, 40, 30);
        camera.lookAt(scene.position);

        // add subtle ambient lighting
        const ambientLight = new THREE.AmbientLight(0x0c0c0c);
        scene.add(ambientLight);

        // add spotlight for the shadows
        const spotLight = new THREE.SpotLight(0xffffff);
        spotLight.position.set(-40, 60, -10);
        spotLight.castShadow = true;
        scene.add(spotLight);

        // add the output of the renderer to the html element
        document.getElementById("WebGL-output").appendChild(renderer.domElement);

        // call the render function
        const step = 0;

        const controls = new function () {
          this.rotationSpeed = 0.02;
          this.numberOfObjects = scene.children.length;

          this.removeCube = function () {
            const allChildren = scene.children;
            const lastObject = allChildren[allChildren.length - 1];
            if (lastObject instanceof THREE.Mesh) {
              scene.remove(lastObject);
              this.numberOfObjects = scene.children.length;
            }
          };

          this.addCube = function () {

            const cubeSize = Math.ceil((Math.random() * 3));
            const cubeGeometry = new THREE.BoxGeometry(cubeSize, cubeSize, cubeSize);
            const cubeMaterial = new THREE.MeshLambertMaterial({color: Math.random() * 0xffffff});
            const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
            cube.castShadow = true;
            cube.name = "cube-" + scene.children.length;


            // position the cube randomly in the scene

            cube.position.x = -30 + Math.round((Math.random() * planeGeometry.parameters.width));
            cube.position.y = Math.round((Math.random() * 5));
            cube.position.z = -20 + Math.round((Math.random() * planeGeometry.parameters.height));

            // add the cube to the scene
            scene.add(cube);
            this.numberOfObjects = scene.children.length;
          };

          this.outputObjects = function () {
            console.log(scene.children);
          }
        };

        const gui = new dat.GUI();
        gui.add(controls, 'rotationSpeed', 0, 0.5);
        gui.add(controls, 'addCube');
        gui.add(controls, 'removeCube');
        gui.add(controls, 'outputObjects');
        gui.add(controls, 'numberOfObjects').listen();

        render();

        function render() {
          stats.update();

          // rotate the cubes around its axes
          scene.traverse(function (e) {
            if (e instanceof THREE.Mesh && e !== plane) {

              e.rotation.x += controls.rotationSpeed;
              e.rotation.y += controls.rotationSpeed;
              e.rotation.z += controls.rotationSpeed;
            }
          });

          // render using requestAnimationFrame
          requestAnimationFrame(render);
          renderer.render(scene, camera);
        }

        function initStats() {

          const stats = new Stats();

          stats.setMode(0); // 0: fps, 1: ms

          // Align top-left
          stats.domElement.style.position = 'absolute';
          stats.domElement.style.left = '0px';
          stats.domElement.style.top = '0px';

          document.getElementById("Stats-output").appendChild(stats.domElement);

          return stats;
        }
      }
      window.onload = init


    </script>
  </body>
</html>
```

GUI의 controls를 이용해서 addCube와 removeCube, outputObjects를 실행할 수 있다.

카메라, 배경 plane, ambient light (주변광), spotlight 4개의 객체가 이미 포함된채 scene이 렌더링된다.

```javascript
this.addCube = function () {

  const cubeSize = Math.ceil((Math.random() * 3));
  const cubeGeometry = new THREE.BoxGeometry(cubeSize, cubeSize, cubeSize);
  const cubeMaterial = new THREE.MeshLambertMaterial({color: Math.random() * 0xffffff});
  const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
  cube.castShadow = true;
  cube.name = "cube-" + scene.children.length;


  // position the cube randomly in the scene

  cube.position.x = -30 + Math.round((Math.random() * planeGeometry.parameters.width));
  cube.position.y = Math.round((Math.random() * 5));
  cube.position.z = -20 + Math.round((Math.random() * planeGeometry.parameters.height));

  // add the cube to the scene
  scene.add(cube);
  this.numberOfObjects = scene.children.length;
};

this.outputObjects = function () {
  console.log(scene.children);
}
};
```

addCube 버튼을 클릭하면 1~3 사이의 랜덤한 사이즈를 가진 새로운 THREE.BoxGeometry 객체가 생성된다. 색상 또한 랜덤하게 지정된다. name 속성을 사용하면 `THREE.Scene.getObjectByName(name)` 함수로  scene에서 객체를 선택해 제어할 수 있다.

```javascript
this.removeCube = function () {
  const allChildren = scene.children;
  const lastObject = allChildren[allChildren.length - 1];
  if (lastObject instanceof THREE.Mesh) {
    scene.remove(lastObject);
    this.numberOfObjects = scene.children.length;
  }
};
```

removeCube를 실행하면 scene의 모든 children 중에서 마지막 object를 제거한다. 추가할 때는 add, 제거할 때는 remove 메소드를 사용한다. 이때 주의해야할 점은, lastObject가 THREE.Mesh 객체인지 확인해 카메라나 조명을 삭제하지 않게 하는 것이다.

outputObjects는 scene.children을 콘솔에 출력한다.

지금까지 살펴본 Scene 관련 함수는 아래와 같다.

- THREE.Scene.add: Scene에 객체를 추가
- THREE.Scene.remove: Scene에서 객체를 삭제
- THREE.Scene.children: Scene에 있는 모든 자식들을 가져옴
- THREE.Scene.getObjectByName: 객체의 이름으로 Scene에서 특정 객체를 가져옴

이 함수들만 알면 scene을 다루는 데 별 무리는 없다. 이외에 편리한 헬퍼 함수를 알면 좋다.

```javascript
function render() {
  stats.update();

  // rotate the cubes around its axes
  scene.traverse(function (e) {
    if (e instanceof THREE.Mesh && e !== plane) {

      e.rotation.x += controls.rotationSpeed;
      e.rotation.y += controls.rotationSpeed;
      e.rotation.z += controls.rotationSpeed;
    }
  });

  // render using requestAnimationFrame
  requestAnimationFrame(render);
  renderer.render(scene, camera);
}
```

위에서 `THREE.Scene.traverse()` 함수가 사용되었다. Scene의 각 자식 객체들마다 호출되는 traverse() 함수에 인수로 함수를 전달할 수 있다. 그 자식의 자식 객체가 있다면, Scene은 트리 구조를 가지므로 객체의 모든 자식 객체들마다 호출된다. traverse() 를 통해 전체 Scene의 그래프를 탐색할 수 있다.

### Scene에 안개 추가

fog 속성은 Scene 전체에 안개 효과를 준다. 농도가 짙어질수록 시야에서 사라진다.

```javascript
scene.fog = new THREE.Fog(0xffffff, 0.015, 100);
```

색상은 흰색(0xffffff), near는 0.015, far는 100으로 설정했다. 이 속성들로 안개가 어디서 시작하고 얼마까지 짙어지는지 정할 수 있다. THREE.Fog 객체에서 안개는 선형적으로 증가한다.

```javascript
scene.fog=new THREE.FogExp2( 0xffffff, 0.015 );
```

위처럼 안개를 적용하면 near와 far 속성을 지정하지 않고 색상과 안개의 밀도(0.015)만 지정한다. THREE.FogExp2에서는 안개가 선형적으로 증가하지 않고 거리에 따라 밀도가 기하급수적으로 커진다.

### overrideMaterial 속성 사용

이 속성을 사용하면 Scene의 모든 객체는 overrideMaterial 속성에 설정된 material을 사용하고 객체 자신에 설정된 material은 무시한다.

```javascript
scene.overrideMaterial = new THREE.MeshLambertMaterial({color: 0xffffff});
```

> #### MeshLambertMaterial
>
> A material for non-shiny surfaces, without specular highlights.

### Scene 요약

| 함수/속성                        | 설명                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| add(object)                      | Scene에 객체를 추가할 때 사용. 객체의 그룹을 만들 때도 이 함수를 사용할 수 있다. |
| children                         | 카메라와 조명을 포함해, Scene에 추가된 모든 객체의 목록을 반환한다. |
| getObjectByName(name, recursive) | 객체를 생성할 때 부여한 name으로 객체를 찾아 반환한다. recursive를 true로 호출하면 Three.js는 특정 이름의 객체를 찾기 위해 모든 객체의 트리를 탐색한다. |
| remove(object)                   | Scene에서 특정 객체를 삭제한다.                              |
| traverse(function)               | children 속성은 Scene의 모든 자식 객체의 목록을 반환한다. traverse 함수로도 자식 객체에 접근할 수 있는데, 모든 자식 객체를 순회하며 지정한 함수에 전달한다. |
| fog                              | Scene에 안개를 설정한다. 안개는 멀리 있는 객체를 숨기게 렌더링한다. |
| overrideMaterial                 | Scene에 있는 모든 객체에 동일한 물질을 적용할 수 있다.       |

### Geometry와 Mesh

여태껏 살펴본 예제에 계속해서 geometry와 mesh가 사용되었다.

```javascript
const sphereGeometry = new THREE.SphereGeometry(4, 20, 20);
const sphereMaterial = new THREE.MeshLambertMaterial({color: 0x7777ff});
const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
```

객체의 모양(Cube, Sphere 등)과 geometry를 정의한다. (THREE.SphereGeometry) 객체가 어떻게 보이는지 (THREE.MeshLambertMaterial) 정의하고, Scene에 추가할 수 있는 Mesh (THREE.Mesh)에서 이 둘을 조합했다. 

> #### Polygon Mesh
>
> ![img](https://upload.wikimedia.org/wikipedia/commons/f/fb/Dolphin_triangle_mesh.png)
>
> ##### Mesh의 요소들
>
> ![Elements of polygonal mesh modeling.](https://upload.wikimedia.org/wikipedia/commons/thumb/6/6d/Mesh_overview.svg/2880px-Mesh_overview.svg.png)
>
> - vertex (점, 꼭지점)
>
>   색상, 노말 벡터 및 텍스처 좌표와 같은 정보에 쓰이는 3D 공간의 위치 좌표
>
> - edge (간선)
>
>   vertex들의 연결선
>
> - face (면)
>
>   간선의 닫힌 집합으로, triangle face는 3개의 간선, quad face는 4개의 간선을 가진다. Polygon은 면들의 집합체다.
>
> - material
>
>   일반적으로 material을 정의하면 렌더링될 때 Mesh의 각 부분에 각자 다른 shader들을 사용할 수 있다.
>
> - shader
>
>   컴퓨터 그래픽에서 셰이더는 3D 장면의 음영(렌더된 이미지에서 적절한 수준의 빛, 어둠, 색의 제작)에 사용되는 컴퓨터 프로그램의 일종이다.

### Geometry의 속성과 함수

Three.js는 3D 장면에서 사용할 수 있는 방대한 Geometry를 제공한다. Material을 추가하고, Mesh를 생성하는 것만으로도 작업이 거의 끝난다.

Three.js와 대부분의 3D 라이브러리에서 Geometry는 3D 공간에서의 점들(vertices)과 이 점들을 연결하는 면들(faces)의 집합을 말한다.

- 정육면체는 8개의 모서리를 갖는다. 각각의 모서리는 x, y, z 축으로 정의될 수 있다. 따라서 정육면체는 3D 공간에서 8개의 꼭지점을 가진다.
- 정육면체는 각 모서리에 꼭지점이 있는 6개의 면으로 구성된다. Three.js에서 면은 항상 삼각형을 만드는 3개의 꼭지점으로 구성된다. 따라서 정육면체의 경우 각 면은 정육면체의 전체 한 면을 만들기 위해 두 개의 삼각형으로 구성된다.

하나의 정육면체 Geometry를 만들기 위해서는 8개의 꼭지점과 6개의 면을 생성해야한다. 그러나 Three.js가 제공하는 Geometry를 사용하면 너비와 높이, 깊이만 지정하면 된다.

### Material

Materials describe the appearance of objects. They are defined in a (mostly) renderer-independent way, so you don't have to rewrite materials if you decide to use a different renderer.

### Mesh 함수와 속성

Mesh를 생성하려면 Geometry와 하나 이상의 Material이 필요하다는 사실을 배웠다. Mesh를 생성하면 이를 Scene에 추가해 렌더링할 수 있다. Mesh가 Scene의 어디에, 어떻게 나타나는지 변경하는 데 사용할 수 있는 여러 속성들이 있다.

| 함수/속성          | 설명                                                         |
| ------------------ | ------------------------------------------------------------ |
| position           | 객체의 위치를 부모의 위치에 상대적인 위치로 결정한다. 대부분의 객체의 부모는 THREE.Scene 객체 또는 THREE.Object3D 객체다. |
| rotation           | 객체의 회전을 설정할 수 있다. Three.js는 또한 각 축별로 회전을 설정할 수 있는 rotateX(), rotateY(), rotateZ() 함수도 제공한다. |
| scale              | x, y, z축으로 객체의 크기를 조절할 수 있다.                  |
| translateX(amount) | 객체를 x축으로 지정된만큼 이동한다.                          |
| translateY(amount) | 객체를 y축으로 지정된만큼 이동한다.                          |
| translateZ(amount) | 객체를 z축으로 지정된만큼 이동한다. translate 함수에 이동할 축을 지정해 이동시킬 수 있는 translateOnAxis(axis, distance) 함수도 있다. |
| visible            | visible = false면 THREE.Mesh가 Three.js에 의해 렌더링되지 않는다. |

#### position 설정 방법

```javascript
cube.position.x = 10;
cube.position.set(10, 3, 1);
cube.position = new THREE.Vector3(10, 3, 1);
```

#### rotation 설정 방법

```javascript
cube.rotation.x = 0.5*Math.PI;
cube.rotation.set(0.5*Math.PI, 0, 0);
cube.rotation = new THREE.Vector3(0.5*Math.PI, 0, 0);
```

각도를 대신 사용하고 싶으면 radian으로 변경하면 됨

```javascript
const degree = 45;
const inRadians = degree * (Math.PI / 180);
```

### 두 가지 카메라의 사용

Three.js는 직교 (orthographic) 카메라와 원근 (perspective) 카메라를 지원한다.

### Perspective Camera

원근 시점(Perspective view)은 가장 자연스러운 뷰다. 카메라에서 멀리 떨어진 객체는 작게 렌더링된다.

#### Orthographic Camera

직교 카메라에서는 카메라와 객체의 거리에 관계 없이 모든 객체가 동일한 크기로 렌더링된다.

```javascript
this.switchCamera = function () {
  if (camera instanceof THREE.PerspectiveCamera) {
    camera = new THREE.OrthographicCamera(window.innerWidth / -16, window.innerWidth / 16, window.innerHeight / 16, window.innerHeight / -16, -200, 500);
    camera.position.set(120, 60, 180);
    camera.lookAt(scene.position);
    this.perspective = "Orthographic";
  } else {
    camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.set(120, 60, 180);

    camera.lookAt(scene.position);
    this.perspective = "Perspective";
  }
};
```

두 가지 카메라를 설정하는 방법이 각각 다르다.

#### THREE.PerspectiveCamera

| Argument | 설명                                                         |
| -------- | ------------------------------------------------------------ |
| fov      | FOV는 Field Of View의 약자로, 카메라의 시야각을 의미한다. 카메라의 위치에 따라서 보여지는 장면이다. 사람의 경우 180도 정도의 FOV를 가지고 있다. 하지만 일반적인 컴퓨터 스크린이 시야를 완전히 보여줄 수 없기 때문이 보통 작은 값이 선택된다. 보통 게임에서 FOV는 약 60 ~ 90도 사이의 값이 선택된다.<br /><br />default: 50 |
| aspect   | 렌더링할 화면의 수평과 수직 간의 종횡비(aspect ratio)를 말한다. 우리의 경우 전체 창을 사용하기 때문에 비율만 사용한다. 다음 이미지에서 볼 수 있듯이 종횡비는 수평 FOV와 수직 FOV 간의 차이를 결정한다.<br /><br /><br />default: window.innerWidth / window.innerHeight |
| near     | near 속성은 Three.js가 Scene을 카메라와 얼마나 가까이에서 렌더링하는지를 정의한다. 일반적으로 카메라의 위치에서 모든 것을 직접 렌더링하기 위해 아주 작은 값으로 설정한다.<br /><br />default: 0.1 |
| far      | far 송성은 카메라에서 볼 수 있는 거리를 정의한다. far 값이 너무 작으면 Scene의 일부가 렌더링되지 않는다. 반대로 너무 크게 설정하면 렌더링 성능에 영향을 미칠 수 있다.<br /><br />default: 100 |
| zoom     | zoom 속성은 Scene을 줌인/줌아웃 할 수 있도록 해준다. 1보다 작은 숫자를 사용하면 줌아웃, 1보다 큰 숫자를 사용하면 줌인 한다. 음수로 설정하면 장면이 거꾸로 뒤집혀 렌더링된다.<br /><br />default: 1 |

카메라의 fov 속성은 수평 FOV를 결정한다. 수직 FOV는 aspect 속성에 의해 결정된다. near 속성은 근거리 면의 위치를 결정하는 데 사용되고 far 속성은 원거리 면의 위치를 결정한다. 원거리 면과 근거리 면 사이의 공간이 렌더링되는 것이다.

#### THREE.OrthographicCamera

직교 카메라를 설정하기 위해서는 다른 속성이 필요하다. 직교 투영에서는 모든 객체가 동일한 크기로 렌더링되기 때문에 종횡비나 FOV를 상관하지 않는다 .직교 카메라를 정의할 때 필요한 것은 렌더링할 cuboid 영역의 정의다.

| Argument | 설명                                                         |
| -------- | ------------------------------------------------------------ |
| left     | Three.js 문서에 카메라 사각뿔의 왼쪽 면(Camera frustum left plane)으로 기술되어 있다. 렌더링되는 왼쪽의 경계로 볼 수 있다. 이 값을 -100으로 설정하면 왼쪽에 있는 아무런 객체도 볼 수 없다. |
| right    | right 속성은 left 속성과 유사한 방법으로 동작하지만, 이 값보다 더 오른쪽에 있는 객체는 렌더링되지 않는다. |
| top      | 렌더링되는 상단의 경계 위치                                  |
| bottom   | 렌더링되는 하단의 경계 위치                                  |
| near     | 카메라의 위치에 기반해 이 시점부터 장면이 렌더링된다.        |
| far      | 카메라의 위치에 기반해 이 시점까지 장면이 렌더링된다.        |
| zoom     | Scene을 줌인, 줌아웃한다. 1보다 작으면 줌아웃, 1보다 크면 줌인, 음수를 지정하면 Scene이 거꾸로 뒤집혀 렌더링된다. 기본값은 1. |

### 특정 지점 바라보기 (lookAt)

카메라가 바라보는 방향은 lookAt 함수로 정의한다.

```javascript
camera.lookAt(new THREE.Vector3(x, y, z));
camera.lookAt(scene.position);
```

카메라가 바라보는 지점을 이동하면, 그 반대방향으로 Scene이 움직이는 것과 같은 효과를 줄 수 있다.

### 그외 Scene 정보

한 번에 여러 개의 Scene을 쓸 수 있는지?

- 가능하다. 웹브라우저는 최대 8개의 renderer를 쓸 수 있도록 한다. 9개를 만들면 첫번째 것이 사라진다.
- 여러 개의 canvas를 쓰는 것은 권장되지 않는다.
- 한 renderer에 여러 scene을 쓰는 것은 가능하다. (각각 scene 카메라 따로 설정)