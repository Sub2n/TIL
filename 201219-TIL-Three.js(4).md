# Three.js Material

Material은 THREE.Geometry와 함께 THREE.Mesh를 구성한다. Material은 Geometry의 외부 모양을 정의하는 스킨과 같다. (금속 재질인지, 투명한지, 와이어프레임으로 보이는지 등)

| 이름                | 설명                                                         |
| ------------------- | ------------------------------------------------------------ |
| MeshBasicMaterial   | Three.js의 기본 Material. Geometry에 간단한 색상을 부여하거나 와이어프레임을 보여줄 때 쓸 수 있다. |
| MeshDepthMaterial   | 카메라로부터의 거리로 Mesh의 색상을 결정할 때 사용하는 Material |
| MeshNormalMaterial  | 일반 벡터(normal vector)의 표면 색상에 기반을 둔 간단한 Material |
| MeshLambertMaterial | 빛을 받아도 반짝이지 않는 객체를 생성할 때 사용<br />https://threejs.org/docs/scenes/material-browser.html#MeshLambertMaterial |
| MeshPhongMaterial   | 빛을 받으면 반짝이는 객체를 생성할 때 사용<br />https://threejs.org/docs/scenes/material-browser.html#MeshPhongMaterial |
| ShaderMaterial      | 꼭지점의 위치와 픽셀 색상을 직접 제어할 수 있는 사용자 정의 셰이더 프로그램을 지정할 수 있게 해줌 |
| LineBasicMaterial   | 색상이 있는 라인을 만드는 THREE.Line Geometry에 쓰임         |
| LineDashMaterial    | LineBasicMaterial과 동일하지만 점선                          |

THREE.RawShaderMaterial이라는 것도 있는데 이건 THREE.BufferedGeomtry와 함께 쓸 때만 사용할 수 있는 특수한 Material이다.

## Material의 공통 속성

- 기본 속성: 가장 자주 사용하는 속성. 객체의 투명도(opacity)나 참조되는 방법(ID, name 등)을 제어할 수 있다.
- 브렌딩 속성: 모든 객체는 브렌딩 속성을 가진다. 객체가 배경과 결합되는 방법을 정의한다.
- 고급 속성: low-level의 WebGL 컨텍스트 객체를 렌더링하는 방법을 제어하는 여러 고급 속성들.

### 기본 속성

THREE.Material의 기본 속성

| 속성        | 설명                                                         |
| ----------- | ------------------------------------------------------------ |
| id          | Material을 식별하는 데 사용되며 생성할 때 할당된다. 첫 Material은 0으로 시작하며 하나 생성될 때마다 1씩 증가한다. |
| uuid        | 고유하게 생성된 id이며 내부적으로 사용된다.                  |
| name        | Material에 이름을 부여할 수 있다. 디버깅 목적으로 사용된다.  |
| opacity     | 객체의 투명도를 정의한다. transparent 속성과 함께 사용한다. 범위는 0 ~ 1 |
| transparent | true면 opacity 설정과 함께 객체를 렌더링함. false면 객체는 투명하지 않고 더 밝은 색상을 가질뿐이다. 알파(투명) 채널을 사용하는 텍스처를 사용하는 경우 transparent를 true로 설정해야 한다. |
| overdraw    | THREE.CanvasRenderer를 사용하면 polygon이 조금 더 크게 렌더링되는데, 그러면 overdraw를 true로 설정해야되 |
| visible     | Material의 가시성을 정의. false면 Scene에서 객체를 볼 수 없음 |
| side        | Geometry의 어느 면에 Material이 적용되는지 정의할 수 있음. default는 객체의 외부에 적용되는 THREE.Frontside. THREE.BackSide로 설정하면 내부에 적용, THREE.DoubleSide는 양면에 적용 |
| needsUpdate | Material의 업데이트를 위해 needsUpdate를 true로 설정하면 Three.js가 캐시를 새로운 Material 속성으로 업데이트한다. |

### 블렌딩(Blending) 속성

Blending은 렌더링하는 색상이 배경색과 상호작용하는 방법을 결정한다. Material의 혼합(블렌딩)에 관련된 속성.

| 이름          | 설명                                                         |
| ------------- | ------------------------------------------------------------ |
| blending      | 객체의 Material이 배경과 혼합하는 방법을 결정한다. 일반 모드는 최상층만 표시하는 THREE.NormalBlending이다. |
| blendsrc      | 표준 혼합 모드 외에 blendsrc와 blenddst, blendequation을 설정해 사용자 정의 혼합 모드를 만들 수도 있다. 이 속성은 객체(source)가 배경(destination)에 혼합되는 방식을 정의한다. 기본값인 THREE.SrcAlphaFactor 설정은 블렌딩에 알파(투명)채널을 사용한다. |
| blenddst      | 배경(destination)이 블렌딩에 사용되는 방법을 정의한다. 기본값은 THREE.OneMinusSrcAlphaFactor로 블렌딩을 위해 소스의 알파채널을 사용하지만, 단 하나만 사용함을 의미한다. |
| blendequation | blendsrc와 blenddst 값을 사용하는 방법을 정의한다. 기본값을 둘을 더하는 것(AddEquation) |

### 고급 속성

WebGL의 내부 동작과 관련된 속성들

| 이름                                                   | 설명                                                         |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| depthTest                                              | 고급 WebGL 속성. 이 속성으로 GL_DEPTH_TEST 매개변수 사용유무 설정 할 수 있음. GL_DEPTH_TEST 매개변수는 픽셀의 depth가 새로운 픽셀 값을 결정하기 위해서 사용되는지 여부를 제어한다. |
| depthWrite                                             | 내부 속성 중 하나로, Material이 WebGL의 depth buffer에 영향을 미치는지 여부를 결정하는 데 사용할 수 있다. |
| polygonOffset, polygonOffsetFactor, polygonOffsetUnits | 이 속성들로 WebGL의 POLYGON_OFFSET_FILL 기능을 제어할 수 있다. (일반적으로 필요하지는 않음) |
| alphaTest                                              | 이 값은 0~1 사이의 특정 값으로 설정될 수 있다. 픽셀이 이 값보다 작은 알파 값을 가지면 화면에 그려지지 않음 |

## 간단한 메시

Material의 속성을 2가지 방법으로 전달할 수 있다.

1. 생성자의 인수로 전달하기

   ```javascript
   const material = new THREE.MeshBasicMaterial({
     color: 0xff0000, // 생성자에 전달하는 값은 hexa 값만 사용 가능
     name: 'material-1', opacity: 0,5,
     transparency: true, ...
   });
   ```

2. 인스턴스를 만들고 속성을 설정하기

   ```javascript
   const material = new THREE.MeshBasicMaterial();
   material.color = new THREE.Color(0xff0000); // 따로 할당할 때는 THREE.Color 객체만 사용해야됨
   material.name = 'material-1';
   material.opacity = 0.5;
   material.transparency = true;
   ```

일반적으로 모든 속성의 값을 알고 있다면 생성자를 사용하는 게 가장 좋은 방법

### THREE.MeshBasicMaterial

MeshBasicMaterial은 **Scene에서의 조명을 고려하지 않는 아주 단순한 Material**이다.

이 Material의 메시는 단순한 평면 폴리곤으로 렌더링되며 Geomerty의 와이어프레임을 표시할 수 있는 옵션이 있다. 공통 속성 외에도 다음 속성들을 사용할 수 있다.

- color
  - Material의 색상 설정
- wireframe
  - Material을 와이어프레임으로 렌더링함. 디버깅에 유용
- wireframeLinewidth
  - 와이어프레임 선의 폭을 결정
- shading
  - 셰이딩이 적용되는 방법을 정의.
  - 가능한 값: THREE.SmoothShading, THREE.NoShading, THREE.FlatShading
  - 기본 값: THREE.SmoothShading으로 부드러운 객체를 만들어 각각의 면을 볼 수 없다.
- vertexColors
  - 각각의 꼭지점에 적용되는 개별 색상 정의. 기본값은 THREE.NoColors.
  - THREE.VertexColors로 설정하면 렌더러는 THREE.Geometry의 color 속성을 고려해 색상을 가져온다. (이 속성은 CanvasRenderer에서는 동작하지 않고 WebGLRenderer에서만 동작)
  - 그라데이션 효과에도 사용 가능
- fog
  - Material이 안개 설정에 영향을 받는지 여부를 결정. (true/false)

### THREE.MeshDepthMaterial

THREE.MeshDepthMaterial에서 객체가 보이는 방법은 조명이나 특정 Material 속성으로 정의되지 않고 카메라로부터 객체까지의 거리에 의해 정의된다.

이 Material을 다른 Material과 결합해 쉽게 페이딩 효과를 만들어낼 수 있다.

이 Material에서는 wireframe, wireframeLinewidth 속성만 의미가 있다.

생성이 쉬워 아무런 인수도 필요하지 않음

카메라의 near 와 far 사이의 거리 = 객체의 밝기와 페이드아웃되는 비율을 정의

거리가 클수록 페이드아웃이 조금됨 / 거리가 작으면 페이드아웃되는 비율이 크다.

### Material의 결합

```javascript
var cubeMaterial = new THREE.MeshDepthMaterial();
var colorMaterial = new THREE.MeshBasicMaterial({
  color: controls.color,
  transparent: true,
  blending: THREE.MultiplyBlending
});
var cube = new THREE.SceneUtils.createMultiMaterialObject(cubeGeometry, [colorMaterial, cubeMaterial]);
cube.children[1].scale.set(0.99, 0.99, 0.99);
```

transparent 속성을 true로 설정해야 blending 속성이 체크되어 객체가 배경과 상호작용하는 방법을 확인한다. 이 경우 배경은 THREE.MeshDepthMaterial로 렌더링된 정육면체다. (자세한 내용은 9장에서...)

### THREE.MeshNormalMaterial

각 면 색상을 다르게 렌더링할 수 있다. 각 면의 색상이 법선 벡터(normal vector)에 기반하기 때문이다. 법선 벡터는 Three.js에서 빛의 반사나 3D 모델에 텍스처를 매칭, 표면의 조명, 그림자, 색상 픽셀에 대한 정보를 제공한다.

THREE.FlatShading, THREE.SmoothShading 적용 가능

### THREE.MeshFaceMaterial

> 스펙에서 사라졌음
>
> 여러개의 Material을 사용하려면 아래와 같이 사용
>
> ```js
> const mesh = new THREE.Mesh( geometry, [ material1, material2 ] );
> ```

Material이라기보다는 Material들의 컨테이너에 가까움

지오메트르의 각 면에 서로 다른 Material을 지정할 수 있게 해준다. 12개의 면을 가진 정육면체(면 하나가 삼각형이므로)의 경우 정육면체의 각 면에 다른 Material을 지정할 수 있다.

```javascript
const group = new THREE.Mesh(); // 큐브를 담을 Mesh 생성

// add all the rubik cube elements
const mats = [];
mats.push(new THREE.MeshBasicMaterial({color: 0x009e60}));
mats.push(new THREE.MeshBasicMaterial({color: 0x009e60}));
mats.push(new THREE.MeshBasicMaterial({color: 0x0051ba}));
mats.push(new THREE.MeshBasicMaterial({color: 0x0051ba}));
mats.push(new THREE.MeshBasicMaterial({color: 0xffd500}));
mats.push(new THREE.MeshBasicMaterial({color: 0xffd500}));
mats.push(new THREE.MeshBasicMaterial({color: 0xff5800}));
mats.push(new THREE.MeshBasicMaterial({color: 0xff5800}));
mats.push(new THREE.MeshBasicMaterial({color: 0xC41E3A}));
mats.push(new THREE.MeshBasicMaterial({color: 0xC41E3A}));
mats.push(new THREE.MeshBasicMaterial({color: 0xffffff}));
mats.push(new THREE.MeshBasicMaterial({color: 0xffffff}));

const faceMaterial = new THREE.MeshFaceMaterial(mats);

for (var x = 0; x < 3; x++) {
  for (var y = 0; y < 3; y++) {
    for (var z = 0; z < 3; z++) {
      const cubeGeom = new THREE.BoxGeometry(2.9, 2.9, 2.9);
      const cube = new THREE.Mesh(cubeGeom, faceMaterial);
      cube.position.set(x * 3 - 3, y * 3, z * 3 - 3);

      group.add(cube);
    }
  }
}
```

