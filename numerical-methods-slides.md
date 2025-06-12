# Numerical Methods for Field Evolution

---

## Third-order Runge-Kutta Method (RK3)
### For Advection Terms

- We define a four-component field $\phi_\mu = (\phi, \vec{\pi}^T)$
- Evolution equation: $\dot{\phi}_\mu = \mathcal{F}_\mu (\phi_\nu)$

---

## RK3 Integration Scheme (Shu & Osher)

1. First step:
   $\phi^{n+1/3}_\mu = \phi^n_\mu + \Delta t \, \mathcal{F}_\mu (\phi^n_\nu)$

2. Second step:
   $\phi^{n+2/3}_\mu = \frac{3}{4} \, \phi^n_\mu + \frac{1}{4} \, \phi^{n+1/3}_\mu + \frac{\Delta t}{4} \, \mathcal{F}_\mu(\phi^{n+1/3}_\nu)$

3. Third step:
   $\phi^{n+1}_\mu = \frac{1}{3} \, \phi^{n}_\mu + \frac{2}{3} \, \phi^{n+2/3}_\mu + \frac{2}{3} \, \Delta t \, \mathcal{F}_\mu(\phi^{n+2/3}_\nu)$

- Transverse projector applied after each substep

---

## Dissipative Update: Metropolis Algorithm

**Key Insight**:
- Diffusive step and noise term implemented via a single Metropolis update
- First moment → diffusive step
- Second moment → noise term
- Guarantees fluctuation-dissipation relations
- Converges to equilibrium distribution: $P[\phi_\mu]\sim \exp(-\mathcal{H}[\phi_\mu]/T)$

---

## Metropolis Update for Scalar Field $\phi$

Trial update:
- $\phi^{\text{trial}}(\vec{x},t+\Delta t) = \phi(\vec{x},t) + q_\mu$
- $\phi^{\text{trial}}(\vec{x}+\hat\mu,t+\Delta t) = \phi(\vec{x}+\hat{\mu},t) - q_\mu$
- $q_\mu = \sqrt{2\Gamma T(\Delta t)}\, \xi$

Where:
- $\xi$ is a Gaussian random variable with unit variance
- $\hat{\mu}$ is an elementary lattice vector

Accept with probability $\min(1,e^{-\Delta\mathcal{H}/T})$

---

## Metropolis Update for Momentum Density $\vec{\pi}$

Trial update:
- $\pi_\nu^{\text{trial}}(\vec{x},t+\Delta t) = \pi_\nu(\vec{x},t) + r_{\nu}^{(\mu)}$
- $\pi_\nu^{\text{trial}}(\vec{x}+\hat\mu,t+\Delta t) = \pi_\nu(\vec{x}+\hat{\mu},t) - r_{\nu}^{(\mu)}$
- $r_{\nu}^{(\mu)} = \sqrt{2\eta T (\Delta t)}\, \zeta_{\nu}^{(\mu)}$

Where:
- $\zeta_\nu^{(\mu)}$ are Gaussian random variables with $\langle \zeta_\mu^{(\alpha)}\zeta_\nu^{(\beta)}\rangle = \delta_{\mu\nu}\delta^{\alpha\beta}$

Accept with probability $\min(1,e^{-\Delta\mathcal{H}/T})$

---

## Transverse Projection

- After a complete sweep, project onto transverse component:
  $\pi_\mu^T (\vec{x},t) = P^T_{\mu\nu} \pi_\nu(\vec{x})$
- Projection carried out in Fourier space
- Ensures $(d-1)$ fluctuating degrees of freedom for momentum density
- Average energy per mode equals $\frac{d-1}{2}T$ rather than $\frac{d}{2}T$

---

## Calculating Energy Changes for Metropolis Steps

For scalar field change at point $\vec{x}$:

$\Delta \mathcal{H}_\phi(\vec{x}) = d[({\phi^{\text{trial}}(\vec{x})})^2 - ({\phi(\vec{x})})^2] - (\phi^{\text{trial}}(\vec{x}) - \phi(\vec{x}))\sum_{\mu=1}^d [\phi(\vec{x}+\hat{\mu}) + \phi(\vec{x}-\hat{\mu})] + \frac{1}{2}m^2[({\phi^{\text{trial}}(\vec{x})})^2 - ({\phi(\vec{x})})^2] + \frac{1}{4}\lambda[({\phi^{\text{trial}}(\vec{x})})^4 - ({\phi(\vec{x})})^4]$

---

## Energy Changes (continued)

For conserving update (transferring charge $q_\mu$ from $\vec{x}+\hat{\mu}$ to $\vec{x}$):

$\Delta \mathcal{H}_\phi (\vec{x}, \vec{x}+\hat \mu) = \Delta \mathcal{H}_\phi(\vec{x}) + \Delta \mathcal{H}_\phi (\vec{x}+\hat \mu) + (q_\mu)^2$

For momentum transfer:

$\Delta \mathcal{H}_\pi (\vec{x}, \vec{x}+\hat \mu) = \frac{1}{\rho}[r_{\nu}^{(\mu)}(\pi^T_\nu(\vec{x})-\pi^T_\nu(\vec{x}+\hat{\mu})) + (r_{\nu}^{(\mu)})^2]$

---

## Implementation Notes

- Metropolis updates performed on a checkerboard pattern
- Timestep for dissipative update can differ from Runge-Kutta timestep
- Transverse projection operator applied after complete lattice sweep
