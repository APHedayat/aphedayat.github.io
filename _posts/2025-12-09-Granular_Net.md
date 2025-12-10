---
layout: post
title: "Granular Net: A Physics-Informed Neural Network for Continuum Modeling of Granular Segregation"
date: 2025-12-09
description: A PINN Approach to Granular Segregation Modeling
tags: AI4Science, SciML, PINNs, GranularMaterials, SegregationModeling
categories: sample-posts
thumbnail: https://aphedayat.github.io/assets/img/post___granular_net/Logo.png
---

Ever wonder why when you pour a bag of mixed nuts, the big ones end up on top? That's granular segregation—particles of different sizes naturally separate when they flow. This is a significant problem in industries like pharmaceuticals and agriculture where uniform mixtures are essential. We explored Physics-Informed Neural Networks (PINNs) as a novel framework for modeling granular segregation, demonstrating both forward solution capabilities and inverse parameter identification from sparse experimental data.

## Project Team

<div class="row mt-3">
    <div class="col-sm-12 mt-3 mt-md-0">
        <div class="text-center">
            <img src="https://aphedayat.github.io/assets/img/post___granular_net/IMG_4102.jpg" alt="Project Team" class="img-fluid rounded z-depth-1" style="max-width: 600px; margin: 0 auto;">
            <div class="row mt-3">
                <div class="col-sm-6">
                    <h5>Amirpasha Hedayat</h5>
                    <p><a href="mailto:ahedayat@umich.edu">ahedayat@umich.edu</a></p>
                </div>
                <div class="col-sm-6">
                    <h5>Amir Nazemi</h5>
                    <p><a href="mailto:nazemi@umich.edu">nazemi@umich.edu</a></p>
                </div>
            </div>
        </div>
    </div>
</div>

## Project Links

- **[Final Report PDF](https://aphedayat.github.io/assets/pdf/post___granular_net/CSE_598__Final-Report.pdf)**
- **[GitHub Repository](https://github.com/amirnzm/GranularNet)**
- **[Presentation Slides](https://aphedayat.github.io/assets/pdf/post___granular_net/CSE598_Project_Slides.pdf)**
- **[Poster](https://aphedayat.github.io/assets/pdf/post___granular_net/Poster_v3.pdf)**

## Background & Challenges

Granular segregation–the spontaneous demixing of particles that differ in size, density, or shape–undermines product uniformity in sectors handling bulk solids, such as polymers, pharmaceuticals, and agricultural products. In hoppers and heap flows used for storage and dosing, changing operating conditions or geometry can substantially alter discharge composition, making scale-up from lab to plant non-trivial.

Traditional continuum models augment the advection–diffusion transport equation with a segregation flux in the direction normal to the free surface, producing a scalar transport PDE for each species' concentration. While these models have achieved quantitative agreement with discrete element method (DEM) simulations and experiments, accurate prediction across geometries and materials still faces two bottlenecks: (a) realistic kinematics are geometry and operating condition dependent; and (b) a universally reliable segregation law is elusive, especially beyond spherical, size-segregating systems.

Physics-Informed Neural Networks (PINNs), introduced by Raissi, Perdikaris, and Karniadakis, incorporate governing PDEs directly into the training objective. By enforcing the residuals of the PDE along with boundary and initial conditions, PINNs can approximate solutions even with limited labeled data. However, to date, PINNs have not been applied to continuum segregation models in granular flows.

## Methodology

We develop PINNs in two phases: (1) a forward PINN that solves the advection–segregation–diffusion PDE with known parameters, and (2) inverse PINNs that learn unknown constitutive parameters or functional forms from sparse experimental data.

### Forward PINN

The forward PINN approximates the concentration field $c_\theta(\tilde{x}, \tilde{z}, \tilde{t})$ using a fully connected multilayer perceptron (MLP) with 4 hidden layers of 64 neurons each. The network is trained by minimizing a composite loss function that enforces the PDE residual, boundary conditions, and initial conditions:

$$\mathcal{L}(\theta) = \lambda_{\text{pde}} \mathcal{L}_{\text{pde}} + \lambda_{\text{bc}} \mathcal{L}_{\text{bc}} + \lambda_{\text{ic}} \mathcal{L}_{\text{ic}}$$

The segregation velocity is fixed to the known analytical form $w_s = \Lambda(1-\tilde{x})g(\tilde{z})(1-c)$ with parameters from the literature.

### Inverse PINNs

We explore three inverse problem formulations:

1. **Learning $\Lambda$**: Treating the segregation parameter as a learnable parameter ($\Lambda$) while maintaining the known functional form.
2. **Learning $A$ and $B$**: Extending to a more general parametric form for segregation velocity with two unknown terms ($A$ and $B$).
3. **Neural Network Closure**: Replacing the entire segregation velocity functional form with a neural network that learns the closure directly from data.

All inverse approaches jointly optimize the concentration network and closure parameters/network while enforcing the governing transport equation.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="https://aphedayat.github.io/assets/img/post___granular_net/Framework.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Granular Net Framework.
</div>

## Results

### Training Convergence

All four models (forward PINN and three inverse variants) demonstrate successful convergence over 20,000 epochs. The forward PINN achieves a final total loss of $1.49 \times 10^{-4}$, with PDE residual losses on the order of $10^{-3}$ to $10^{-4}$, confirming accurate enforcement of physical constraints.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="https://aphedayat.github.io/assets/img/post___granular_net/Results/Loss_Plots.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Training loss history for all four PINN models showing convergence of PDE residual, boundary condition, and data-fitting losses.
</div>

### Concentration Field Predictions

The forward PINN successfully captures the expected segregation pattern: small particles accumulate near the bottom of the flowing layer, while large particles dominate near the surface. The segregation becomes more pronounced downstream and evolves over time from the initial well-mixed state to the final segregated profile.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="https://aphedayat.github.io/assets/img/post___granular_net/Results/PINN_Plots.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Concentration field evolution for all PINN models at six time points, showing successful capture of segregation dynamics.
</div>

### Prediction Accuracy

The forward PINN achieves excellent accuracy with RMSE = $0.0030$ and MAE = $0.0022$ on test data. The inverse models achieve even higher accuracy on experimental data:

- **PINN_Lambda**: RMSE = $0.0015$, MAE = $0.0009$
- **PINN_AB**: RMSE = $0.0015$, MAE = $0.0010$
- **PINN_NN**: RMSE = $0.0028$, MAE = $0.0015$

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="https://aphedayat.github.io/assets/img/post___granular_net/Results/Predicted_vs_Actual.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Predicted versus actual concentration scatter plots for all four PINN models, demonstrating excellent agreement with ground truth.
</div>

### Parameter Discovery

The inverse PINN for learning $\Lambda$ successfully recovers parameter values from sparse experimental data. Starting from an initial value of $\Lambda = 0.3949$, the parameter converges to $\Lambda = 1.2007$, matching the ground truth value of $1.2$ with an error of only $0.0007$ ($0.06\%$).

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="https://aphedayat.github.io/assets/img/post___granular_net/Results/Segregation_Velocity_Comparison.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Parameter evolution and segregation velocity comparison showing successful recovery of constitutive parameters and learned closure laws.
</div>

## Key Contributions

Our primary contributions are:

**Forward Solution Capability:** We established that PINNs can accurately solve the advection-segregation-diffusion equation for granular segregation without requiring labeled training data (purely unsupervised).

**Parameter Identification:** We demonstrated that PINNs can simultaneously learn both the concentration field and unknown constitutive parameters. The inverse PINN successfully recovered parameter values that explain experimental observations while maintaining physical consistency through the PDE constraints.

**Closure Discovery:** Most significantly, we showed that neural network closures can be learned directly from data while enforcing the governing transport equation. The learned closure automatically captures the complex dependencies of segregation velocity on spatial coordinates, shear rate, and concentration without assuming a specific analytical form.

## Broader Impact

This work contributes to the broader goal of developing data-driven, physics-informed models for granular materials. The ability to discover constitutive laws from sparse experimental data while maintaining physical consistency has implications beyond segregation modeling.

## Future Directions

Several opportunities for future work include:

- **Generalization**: Testing model performance across different geometries, particle size ratios, and operating conditions. Possibly combining with Neural Operator to improve extrapolation capabilities of the model.
- **Uncertainty Quantification**: Incorporating Bayesian approaches or ensemble methods to provide uncertainty estimates.
- **Interpretability**: Exploring symbolic regression to discover interpretable functional forms for segregation velocity (e.g. SINDy).

---

### Acknowledgements

*This work was completed as part of CSE 598: AI for Science at the University of Michigan. We thank our instructors, Professor Alexander Rodríguez and Diana Gomez, for their valuable feedback and support.*
