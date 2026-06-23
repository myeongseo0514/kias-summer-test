# 물리 운동 시뮬레이션

브라우저에서 바로 실행할 수 있는 물리 운동 시뮬레이션 모음입니다. 이중진자, 원운동, 포물선 운동을 각각 독립 HTML 페이지로 제공합니다.

## 실행 방법

별도의 설치나 빌드가 필요 없습니다. 원하는 HTML 파일을 브라우저에서 열면 됩니다.

```bash
# Windows
start index.html
start circular-motion.html
start projectile-motion.html

# macOS
open index.html
open circular-motion.html
open projectile-motion.html
```

또는 파일을 더블 클릭하여 기본 브라우저로 열 수 있습니다.

## 기능

### 제공 시뮬레이션

| 파일 | 시뮬레이션 | 주요 내용 |
|------|------------|-----------|
| `index.html` | 이중진자 운동 | Lagrange 방정식, RK4 적분, 에너지 보존 검증 |
| `circular-motion.html` | 원운동 | 등속 원운동, 속도 벡터, 구심가속도, 구심력 |
| `projectile-motion.html` | 포물선 운동 | 초기 속도, 발사각, 중력, 비행 시간, 도달 거리 |

### 조절 가능한 파라미터

`index.html`의 이중진자 시뮬레이션에서는 다음 값을 조절할 수 있습니다.

| 파라미터 | 설명 | 범위 |
|---------|------|------|
| m₁ | 첫 번째 질량 (kg) | 0.1 ~ 5 |
| m₂ | 두 번째 질량 (kg) | 0.1 ~ 5 |
| L₁ | 첫 번째 막대 길이 (m) | 0.3 ~ 2 |
| L₂ | 두 번째 막대 길이 (m) | 0.3 ~ 2 |
| g | 중력 가속도 (m/s²) | 1 ~ 20 |

슬라이더로 값을 변경하면 시뮬레이션이 즉시 반영됩니다.

### 인터랙션

- **초기화** — 기본 초기 각도로 되돌리고 궤적을 지웁니다.
- **일시정지 / 재생** — 시뮬레이션을 멈추거나 다시 시작합니다.
- **마우스 드래그** — 진자의 질량(m₁, m₂)을 클릭하여 드래그하면 초기 위치를 설정할 수 있습니다.

### 시각화

- 두 개의 막대와 질량(주황: m₁, 파랑: m₂)
- 두 번째 질량의 이동 궤적(파란색 선)
- 질량 크기에 비례한 원 크기

## 물리 모델

이중진자는 질량 $m_1$, $m_2$와 길이 $L_1$, $L_2$의 두 막대로 구성됩니다. 각도 $\theta_1$, $\theta_2$는 수직 아래 방향을 기준으로 측정합니다.

### 좌표와 Lagrangian

두 질량의 위치는 다음과 같이 표현됩니다.

$$
x_1 = L_1 \sin\theta_1,\quad y_1 = L_1 \cos\theta_1
$$

$$
x_2 = L_1 \sin\theta_1 + L_2 \sin\theta_2,\quad y_2 = L_1 \cos\theta_1 + L_2 \cos\theta_2
$$

Lagrangian은 운동 에너지 $T$와 위치 에너지 $V$의 차이로 정의됩니다.

$$
\mathcal{L} = T - V
$$

$$
T = \frac{1}{2} m_1 v_1^2 + \frac{1}{2} m_2 v_2^2
$$

$$
V = m_1 g y_1 + m_2 g y_2 = m_1 g L_1 \cos\theta_1 + m_2 g (L_1 \cos\theta_1 + L_2 \cos\theta_2)
$$

Lagrange 방정식은 각 일반화 좌표 $\theta_i$에 대해 다음을 만족합니다.

$$
\frac{d}{dt}\left(\frac{\partial \mathcal{L}}{\partial \dot{\theta}_i}\right) - \frac{\partial \mathcal{L}}{\partial \theta_i} = 0,\quad i = 1, 2
$$

### 상태 방정식

상태 벡터를 $\mathbf{s} = (\theta_1, \theta_2, \omega_1, \omega_2)$로 정의하면, 각속도와 각가속도는 다음 관계를 따릅니다.

$$
\dot{\theta}_1 = \omega_1,\quad \dot{\theta}_2 = \omega_2
$$

공통 분모는 다음과 같습니다.

$$
D = 2m_1 + m_2 - m_2 \cos(2\theta_1 - 2\theta_2)
$$

각가속도 $\alpha_1 = \dot{\omega}_1$, $\alpha_2 = \dot{\omega}_2$는 다음과 같이 계산됩니다.

$$
\alpha_1 = \frac{-g(2m_1 + m_2)\sin\theta_1 - m_2 g \sin(\theta_1 - 2\theta_2) - 2\sin(\theta_1 - \theta_2)\, m_2 \left(\omega_2^2 L_2 + \omega_1^2 L_1 \cos(\theta_1 - \theta_2)\right)}{L_1 \, D}
$$

$$
\alpha_2 = \frac{2\sin(\theta_1 - \theta_2)\left(\omega_1^2 L_1 (m_1 + m_2) + g(m_1 + m_2)\cos\theta_1 + \omega_2^2 L_2 m_2 \cos(\theta_1 - \theta_2)\right)}{L_2 \, D}
$$

### RK4 수치 적분

4차 Runge-Kutta(RK4) 방법으로 상태 방정식 $\dot{\mathbf{s}} = f(\mathbf{s})$를 적분합니다.

$$
\mathbf{k}_1 = f(\mathbf{s}_n)
$$

$$
\mathbf{k}_2 = f\left(\mathbf{s}_n + \frac{\Delta t}{2}\mathbf{k}_1\right)
$$

$$
\mathbf{k}_3 = f\left(\mathbf{s}_n + \frac{\Delta t}{2}\mathbf{k}_2\right)
$$

$$
\mathbf{k}_4 = f\left(\mathbf{s}_n + \Delta t\,\mathbf{k}_3\right)
$$

$$
\mathbf{s}_{n+1} = \mathbf{s}_n + \frac{\Delta t}{6}\left(\mathbf{k}_1 + 2\mathbf{k}_2 + 2\mathbf{k}_3 + \mathbf{k}_4\right)
$$

시뮬레이션 설정:

- 시간 간격: $\Delta t = \dfrac{1}{120}\,\mathrm{s}$
- 프레임당 2회 적분하여 안정성을 높입니다.

### 에너지 보존 검증

화면에 총 에너지와 드리프트(%)를 실시간으로 표시합니다.

**운동 에너지**

$$
K = \frac{1}{2} m_1 v_1^2 + \frac{1}{2} m_2 v_2^2
$$

**위치 에너지**

$$
U = m_1 g y_1 + m_2 g y_2
$$

**총 에너지**

$$
E = K + U
$$

**드리프트** (초기 에너지 $E_0$ 대비 변화율)

$$
\text{Drift} = \frac{E - E_0}{E_0} \times 100\,\%
$$

드리프트가 작게 유지되면 수치 적분이 물리 법칙을 잘 만족한다는 의미입니다.

## 파일 구조

```
.
├── index.html               # 이중진자 시뮬레이션
├── circular-motion.html     # 원운동 시뮬레이션
├── projectile-motion.html   # 포물선 운동 시뮬레이션
├── AGENTS.md                # 프로젝트 요구사항
└── README.md                # 이 문서
```

## 기술 스택

- HTML5 Canvas — 렌더링
- Vanilla JavaScript — 물리 계산 및 UI
- 외부 라이브러리 없음

## 참고

이중진자는 카오스 시스템의 대표적인 예입니다. 초기 조건에 매우 민감하게 반응하여, 약간의 각도 차이만으로도 궤적이 크게 달라집니다. 원운동과 포물선 운동 페이지에서는 기본적인 해석 역학 관계를 시각적으로 확인할 수 있습니다.
