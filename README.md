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

세 시뮬레이션은 모두 질점을 대상으로 하며, 별도로 언급하지 않는 한 공기 저항과 마찰은 무시합니다. 중력 가속도 $g$는 시간과 위치에 따라 변하지 않는 상수로 둡니다.

### 1. 이중진자 운동

이중진자는 길이 $L_1$, $L_2$인 질량 없는 두 막대와 질점 $m_1$, $m_2$로 구성됩니다. 각도 $\theta_1$, $\theta_2$는 수직 아래 방향을 기준으로 측정합니다. 화면 좌표는 아래쪽이 양수이지만, 물리 에너지 설명에서는 일반적인 관례에 따라 위쪽을 양의 $z$축으로 둡니다.

#### 좌표

첫 번째 질점의 위치는

$$
x_1 = L_1 \sin\theta_1,\quad z_1 = -L_1 \cos\theta_1
$$

두 번째 질점의 위치는

$$
x_2 = L_1 \sin\theta_1 + L_2 \sin\theta_2
$$

$$
z_2 = -L_1 \cos\theta_1 - L_2 \cos\theta_2
$$

입니다. 따라서 두 질점의 속도 제곱은 다음과 같습니다.

$$
v_1^2 = L_1^2 \dot{\theta}_1^2
$$

$$
v_2^2 =
L_1^2 \dot{\theta}_1^2
+ L_2^2 \dot{\theta}_2^2
+ 2L_1L_2\dot{\theta}_1\dot{\theta}_2\cos(\theta_1-\theta_2)
$$

#### 에너지와 Lagrangian

운동 에너지 $T$는 두 질점의 병진 운동 에너지 합입니다.

$$
T = \frac{1}{2}m_1v_1^2 + \frac{1}{2}m_2v_2^2
$$

위치 에너지 $U$는 $U = mgz$를 사용합니다.

$$
U = m_1gz_1 + m_2gz_2
$$

즉,

$$
U = -(m_1+m_2)gL_1\cos\theta_1 - m_2gL_2\cos\theta_2
$$

Lagrangian은 운동 에너지와 위치 에너지의 차이입니다.

$$
\mathcal{L} = T - U
$$

각 일반화 좌표 $\theta_i$는 Euler-Lagrange 방정식을 만족합니다.

$$
\frac{d}{dt}
\left(
\frac{\partial \mathcal{L}}{\partial \dot{\theta}_i}
\right)
- \frac{\partial \mathcal{L}}{\partial \theta_i}
= 0,\quad i=1,2
$$

#### 상태 방정식

상태 벡터를

$$
\mathbf{s} = (\theta_1,\theta_2,\omega_1,\omega_2)
$$

로 두고, $\omega_1=\dot{\theta}_1$, $\omega_2=\dot{\theta}_2$라고 하면

$$
\dot{\theta}_1 = \omega_1,\quad \dot{\theta}_2 = \omega_2
$$

입니다. 공통 분모는

$$
D = 2m_1 + m_2 - m_2\cos(2\theta_1-2\theta_2)
$$

이며, 각가속도 $\alpha_1=\dot{\omega}_1$, $\alpha_2=\dot{\omega}_2$는 다음과 같이 계산됩니다.

$$
\alpha_1 =
\frac{
-g(2m_1+m_2)\sin\theta_1
-m_2g\sin(\theta_1-2\theta_2)
-2m_2\sin(\theta_1-\theta_2)
\left(
\omega_2^2L_2+\omega_1^2L_1\cos(\theta_1-\theta_2)
\right)
}
{L_1D}
$$

$$
\alpha_2 =
\frac{
2\sin(\theta_1-\theta_2)
\left(
\omega_1^2L_1(m_1+m_2)
+g(m_1+m_2)\cos\theta_1
+\omega_2^2L_2m_2\cos(\theta_1-\theta_2)
\right)
}
{L_2D}
$$

이 방정식은 두 각도가 서로 결합된 비선형 미분방정식입니다. 그래서 초기 각도나 각속도를 조금만 바꾸어도 시간이 지나면서 궤적이 크게 달라지는 카오스적 특성이 나타납니다.

#### RK4 수치 적분

이중진자 운동은 해석적으로 간단한 닫힌 형태의 해를 얻기 어렵기 때문에, 시뮬레이션에서는 상태 방정식

$$
\dot{\mathbf{s}} = f(\mathbf{s})
$$

을 4차 Runge-Kutta 방법으로 적분합니다.

$$
\mathbf{k}_1 = f(\mathbf{s}_n)
$$

$$
\mathbf{k}_2 =
f\left(
\mathbf{s}_n+\frac{\Delta t}{2}\mathbf{k}_1
\right)
$$

$$
\mathbf{k}_3 =
f\left(
\mathbf{s}_n+\frac{\Delta t}{2}\mathbf{k}_2
\right)
$$

$$
\mathbf{k}_4 =
f\left(
\mathbf{s}_n+\Delta t\,\mathbf{k}_3
\right)
$$

$$
\mathbf{s}_{n+1}
=
\mathbf{s}_n
+ \frac{\Delta t}{6}
\left(
\mathbf{k}_1+2\mathbf{k}_2+2\mathbf{k}_3+\mathbf{k}_4
\right)
$$

전체 역학적 에너지는 이상적으로

$$
E = T + U
$$

로 보존됩니다. 수치 적분에서는 작은 오차가 누적될 수 있으므로, 초기 에너지 $E_0$에 대한 상대 변화율을 드리프트로 볼 수 있습니다.

$$
\text{Drift}
=
\frac{E-E_0}{E_0}\times 100\,\%
$$

### 2. 원운동

원운동 시뮬레이션은 반지름 $R$인 원 위를 질량 $m$인 물체가 일정한 각속도 $\omega$로 움직이는 등속 원운동을 다룹니다.

#### 각도와 위치

초기 위상을 $\theta_0$라고 하면 시간 $t$에서의 각도는

$$
\theta(t) = \theta_0 + \omega t
$$

입니다. 원의 중심을 원점으로 잡으면 위치 벡터는

$$
\mathbf{r}(t)
=
\begin{pmatrix}
x(t) \\
y(t)
\end{pmatrix}
=
\begin{pmatrix}
R\cos\theta(t) \\
R\sin\theta(t)
\end{pmatrix}
$$

입니다. 각속도 $\omega$가 양수이면 반시계 방향, 음수이면 시계 방향으로 회전합니다.

#### 속도

속도는 위치를 시간에 대해 미분하여 얻습니다.

$$
\mathbf{v}(t)
=
\frac{d\mathbf{r}}{dt}
=
\begin{pmatrix}
-R\omega\sin\theta(t) \\
R\omega\cos\theta(t)
\end{pmatrix}
$$

속도 벡터는 항상 원의 접선 방향입니다. 속력은

$$
v = |\mathbf{v}| = R|\omega|
$$

입니다.

#### 가속도와 구심력

가속도는 속도를 다시 시간에 대해 미분하여 얻습니다.

$$
\mathbf{a}(t)
=
\frac{d\mathbf{v}}{dt}
=
\begin{pmatrix}
-R\omega^2\cos\theta(t) \\
-R\omega^2\sin\theta(t)
\end{pmatrix}
$$

이는 위치 벡터와 반대 방향입니다.

$$
\mathbf{a}(t) = -\omega^2\mathbf{r}(t)
$$

따라서 가속도는 항상 원의 중심을 향하며, 크기는

$$
a_c = R\omega^2
$$

입니다. 이 가속도를 만들기 위해 필요한 힘이 구심력입니다.

$$
F_c = ma_c = mR\omega^2
$$

한 바퀴를 도는 데 걸리는 주기 $T$와 진동수 $f$는 다음과 같습니다.

$$
T = \frac{2\pi}{|\omega|},\quad
f = \frac{1}{T} = \frac{|\omega|}{2\pi}
$$

### 3. 포물선 운동

포물선 운동 시뮬레이션은 초기 높이 $h$에서 속력 $v_0$, 발사각 $\theta$로 던진 물체의 2차원 운동을 다룹니다. 공기 저항이 없다고 가정하면 수평 방향과 수직 방향 운동을 분리해서 계산할 수 있습니다.

#### 초기 속도 성분

초기 속도는 수평 성분과 수직 성분으로 나뉩니다.

$$
v_{0x} = v_0\cos\theta
$$

$$
v_{0y} = v_0\sin\theta
$$

수평 방향에는 힘이 없으므로 수평 속도는 일정하고, 수직 방향에는 중력만 작용합니다.

$$
a_x = 0,\quad a_y = -g
$$

#### 위치와 속도

시간 $t$에서의 위치는

$$
x(t) = v_{0x}t
$$

$$
y(t) = h + v_{0y}t - \frac{1}{2}gt^2
$$

입니다. 속도 성분은

$$
v_x(t) = v_{0x}
$$

$$
v_y(t) = v_{0y} - gt
$$

이고, 순간 속력은

$$
v(t) =
\sqrt{
v_x(t)^2 + v_y(t)^2
}
$$

입니다.

#### 궤적 방정식

$x(t)=v_{0x}t$에서 $t=x/v_{0x}$를 대입하면 시간 변수를 제거한 궤적 방정식을 얻습니다.

$$
y(x)
=
h
+ x\tan\theta
- \frac{g x^2}{2v_0^2\cos^2\theta}
$$

$x^2$ 항의 계수가 음수이므로 궤적은 아래로 열린 포물선입니다.

#### 비행 시간, 최고 높이, 도달 거리

물체가 지면에 닿는 순간은 $y(t)=0$을 만족합니다.

$$
0 = h + v_{0y}t - \frac{1}{2}gt^2
$$

양의 해를 선택하면 비행 시간 $T$는

$$
T =
\frac{
v_{0y}+\sqrt{v_{0y}^2+2gh}
}
{g}
$$

입니다. 도달 거리 $R$은 비행 시간 동안 이동한 수평 거리입니다.

$$
R = v_{0x}T
$$

최고점에서는 수직 속도가 0이므로

$$
v_y(t_{\text{peak}})=0
$$

이고,

$$
t_{\text{peak}} = \frac{v_{0y}}{g}
$$

입니다. 최고 높이 $H$는

$$
H = h + \frac{v_{0y}^2}{2g}
$$

입니다.

#### 에너지 관점

공기 저항이 없다면 역학적 에너지는 보존됩니다.

$$
E = \frac{1}{2}mv^2 + mgy
$$

수평 속도는 변하지 않지만, 수직 속도는 상승 중에는 줄어들고 하강 중에는 커집니다. 위치 에너지와 운동 에너지가 서로 변환되면서 전체 에너지는 일정하게 유지됩니다.

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
