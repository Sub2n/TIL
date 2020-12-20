# Three.js에서 사용할 수 있는 다양한 광원

광원 없이는 아무 것도 렌더링되는 것을 볼 수 없다. WebGL 자체는 광원을 지원하지 않지만 Three.js는 광대한 양의 광원을 지원하고 있다.

- Three.js에서 사용할 수 있는 광원들
- 특정 광원의 사용이 필요한 때
- 광원의 행동을 조율하고 조정하기
- 렌즈 플레어 효과(lens flare) (햇빛 효과)

### Three.js가 제공하는 다양한 빛

| 이름                   | 설명                                                         |
| ---------------------- | ------------------------------------------------------------ |
| THREE.AmbientLight     | 기본 광원. Scene에 있는 객체의 현재 색상에 빛의 색상이 더해진다. |
| THREE.PointLight       | 한 점에서 모든 방향으로 확산되는 빛. 그림자를 만드는 데 사용하지 못함 |
| THREE.SpotLight        | 램프나 천장의 등, 횃불같은 원뿔 효과를 가짐. 그림자를 만들 수 있음 |
| THREE.DirectionalLight | 무한광(infinite light)이라고도 불린다. DirectionalLight의 광선은 태양광과 비슷하게 보인다. 그림자를 만들 수 있다. |
| THREE.HemisphereLight  | 특별한 광원으로, 표면 반사나 희미한 하늘을 흉내내서 자연스러운 외부광을 만드는 데 사용된다. 그림자를 만들 수 없다. |
| THREE.AreaLight        | 공간에서 한 지점 대신 빛을 발산하는 공감을 지정할 수 있다. 그림자를 만들 수 없다. |
| THREE.LensFlare        | 광원은 아니지만 Scene의 광원에 렌즈 플레어 효과(햇빛 효과)를 줄 수 있다. |

이 중 기본 광원은 

- THREE.AmbientLight
- THREE.PointLight
- THREE.SpotLight
- THREE.DirectionalLight

이다. 이 광원들은 모두 THREE.Light 객체를 확장한 것으로 공통된 기능을 제공한다.

나머지 THREE.HemisphereLight, THREE.AreaLight, THREE.LensFlare 등은 주로 특별한 상황에서만 사용한다.

### 기본 광원

### THREE.AmbientLight	

THREE.AmbientLight를 생성하면 색상이 전체에 적용된다. Ambient Light는 특정한 빛의 시작점이 없으며 그러므로 그림자를 만들지 않는다. 객체의 모양에 관계 없이 동일한 색상을 주기 때문에 일반적으로 단독으로 사용되지 않는다. 보통 SpotLight나 DirectionalLight와 함께 사용하며 그림자를 부드럽게 하거나 Scene에 추가적인 색상을 더하는 데 사용한다.