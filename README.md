Hands_on_bridgedrive

22/06/2026

DiffusionDrive does not start with pure noise, but rather with an anchor that has had a small amount of noise added for denoising.

BridgeDrive mathematically formalizes the planning task as a Diffusion Bridge. This ensures that the forward and reverse denoising processes are perfectly symmetrical.

<img width="1446" height="604" alt="BridgeDrive_algorithm" src="https://github.com/user-attachments/assets/097d8c60-255c-4f59-8142-46c06f4f4a32" />

BridgeDrive is compatible with efficient ODE solvers, enabling real-time deployment.

<br>

24/06/2026

The planning task in autonomous driving can be formulated as predicting future trajectories of the ego-vehicle based on raw sensor inputs.

(1) Temporal speed waypoints

(2) Geometric path waypoints

Highly different between Open-loop and closed-loop setting. CARLA is most widely used platform.

Mathematically, the forward process can be concluded as:

$$dx_t=f(t)x_tdt+g(t)dw_t,x_0\sim p_d,(1)$$

Caculating the loss function:

$$\frac{dx_t}{dt} = f(t)x_t - \frac{g(t)^2}{2} \mathbf{\nabla_{x_t}\log q(x_t)}$$

$$\nabla_{x_t}\log q(x_t|x_0) = -\frac{x_t - \alpha_t x_0}{\sigma_t^2} = \frac{\alpha_t x_0 - x_t}{\sigma_t^2}$$

$$\min_\theta \mathbb{E} [w(t) \lVert x_\theta(x_t,t) - x_0 \rVert^2]$$

25/06/2026

To incorporate anchors into diffusion models in a principled way, we propose to factorize the joint distribution of the ground-truth trajectory x, anchor y, and guidance information z as

$$p_d(x,y,z) = p_d(x|y,z)p_d(y|z)p_d(z)$$

The model constructs a direct bridge between the ground-truth trajectory ($x_0 = x$) and the anchor ($x_T = y$). Because the SDE is linear, it yields an analytical Gaussian transition kernel, enabling efficient, simulation-free training:

$$q(x_t|x_0,x_T) = \mathcal{N}(x_t | a_t x_T + b_t x_0, c_t^2 I)$$

Unlike DiffusionDrive, both forward and reverse paths perfectly align at $x_0$. The denoiser is trained to reverse this exact bridge by minimizing the error against the ground truth:

$$\min_\theta \mathbb{E} \left[ w(t) \lVert x_\theta(x_t, t, x_T, z) - x_0 \rVert^2 \right]$$
