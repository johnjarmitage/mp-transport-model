---
# File metadata may be provided as frontmatter YAML
title: Article - 1D Microplastic and sediment transport model
subtitle: A test of a simple transport law for microplastic in fluvial systems
description: A notebook to test a method for integrating microplastics within landscape evolution models
date: 2023-10-20
tags:
  - Shallow water equations
  - Sediment transport
---

### Abstract


### Introduction

It is not controversial to state that there is an increasing awareness of the threat of plastic pollution on life on Earth. Plastics are a highly durable material and cheap to produce. They are used in nearly all industrial processes and have entered the atmosphere, hydrosphere, and solid Earth. This plastic pollution could be used as a stratigraphic marker for the Anthropocene. Plastics once used are either recycled, incinerated, or dumped. It is estimated that roughly 9% of plastics created are recycled, and about 12% are incinerated (Geyer et al., 2017). What is left is discarded either in landfill or in open pits. A further pathway for plastic to enter the environment is through wastewater treatment. Small plastic nodules are used within many sanitary products, and these can then enter directly into the sea or river systems if the waste is not treated.

Estimates of total plastic show an exponential growth in production. In 2015 it was estimated that 407 Mt of primary plastic (virgin plastic created from raw materials) entered into a use phase, while 302 Mt left the use-phase (Geyer et al., 2017). Of that stock of plastic that is estimated to have left use, roughly 80% will enter landfill or be dumped in open pits. Plastic that is not contained within managed landfill is termed mismanaged plastic waste (MPW) and it is estimated that in 2015 there was between 65 and 99 Mt of MPW (Lebreton & Andrady, 2019). Of this, estimates are that somewhere between 0.8 and 4 Mt per year enters the ocean via the riverine network (Figure 1) (Lebreton et al., 2017; Schmidt et al., 2017; Meijer et al., 2021). The estimates of plastic waste that enters the ocean differ on the assumptions for the spatial distribution of MPW and the impact of climate on river run-off on the delivery of plastic to the oceans. This means that the importance of small river catchments versus large rivers is still uncertain. This uncertainty is demonstrated by for example a local study of microplastic contamination within the Mersey and Irwel rivers in England. These two small rivers were found to contain roughly half a million particles of microplastic per meter, which makes them one of the most polluted rivers globally (Hurley et al., 2018). This watershed however does not suffer from open tipping of plastic, and there is no strong evidence for mismanaged water treatment. Therefore, it is possible that either significantly more plastic that previously thought is stored within rivers, or significantly more plastic that estimated enters the oceans.

```{note}
Some text on the motivation of this work based on the statement above.
```

### Brief review of existing models

There is a surprising lack of numerical models for the transport of microplastics. The process-based model INCA-Contaminants was developed with the aim to model the transport of microplastics (Nizzetto & Bussi et al., 2016). This model divides the river catchment into reaches and within each reach assumes sediment is transported along the reach with the water flow and with a fall velocity given by the density of the microplastic following Equation 1. This fall velocity is, as has been discussed in Waldschläger and Schüttrumpf  (2019a), an overestimate of the fall velocity. However, by how much this first order calculation overestimates the settling of microplastic is hard to estimate given that settling is affected by weather and in particular biofilm growth. These effects will act to increase the settling velocity towards the estimate made in INCA-Contaminants.

INCA-Contaminants was used to explore the potential microplastic transport within the River Thames, UK (Nizzetto & Bussi et al., 2016). Unfortunately, there are no observations for microplastics within the water column or within the sediments for any comparison between the model prediction. In effect the model treats microplastic as a particularly light sediment. In this respect, the assumption might be valid, if microplastics correlate with clay deposition in all catchments. This assumption has however not been tested.

The model INCA-contaminates is a slight modification on INCA-seds (Lazar et al., 2010). These models solve for the transport of particles by reducing the spatial dimensions to a one dimension by splitting the catchment into reaches and modelling the various input pathways to each reach (Lazar et al., 2010). This means that the model reduces to a series of ordinary differential equations and the spatial variability in sediment or microplastic deposition cannot be captured. Instead the focus is on capturing the flux of microplastic as it leaves the catchment at a particular gauging station or other point of interest (Nizzetto & Bussi et al., 2016). This means the long-term storage of microplastic within the floodplains or other three-dimensional natural barriers cannot be captured.

A different approach would be to model the full problem of the flow of surface water, as is taken to model particles of car tires (Unice et al., 2019). These particles are assumed to travel with runoff and hence a hydrological model, DELFT3D-WAQ, is used to model the freshwater flow. Based on the flow pathways and estimates of removal of the particles from these flow pathways due to water treatment and deposition within the sediment, the amount of tire particles that remain in the environment can be estimated (Unice et al., 2019). This sort of model is essentially a post-processing of a hydrological model that solves for the shallow water equations within the terrestrial environment. As such it might not be appropriate for exploring how microplastics get transported, stored, and remobilized within the fluvial environment.

As a compromise between the reduced dimension model (INCA) and the full Navier-Stokes problem (DEFLT3D), the transport of microplastics could be captured using reduced complexity models as applied to tracking contaminated sediment or to study the source-to-sink pathway of sediment released from earthquakes (Coulthard & Macklin, 2003; Xie et al., 2022). These models use the transport equations for sediments developed by Wilcock and Crowe  (2003) to link the water flux to sediment flux to capture the transport of multiple grain sizes down system (CAESAR-Lisflood; Coulthard et al., 2013). Subsequently, certain grains are tracked as they flow through the system. In this case both models are used to track sediments and not microplastics, however there is the potential in this approach for tracking microplastic as it migrates down the fluvial system. The advantage is that the spatial distribution of deposition can be captured, in particular deposition in floodplains. For example, from modelling lead contamination in sediments it was found that avulsions within the depositional environment lead to a significant reworking of previous deposits and a release of contaminants into the watershed (Coulthard & Macklin, 2003). Given that microplastics are found within riverbank deposits, avulsions would shave a similar impact on microplastic contamination.

### Modelling microplastic as a sediment

#### Water flux

To include microplastics within a sediment transport model we build upon the framework of Caesar-Lisflood, where the flow of water is solved for assuming the diffusive-wave approximation to the shallow water equations, and that the microplastic is eroded from the bed above a critical threshold. The full Saint-Venant shallow water equations can be written as,
```{math}
:label: stv-mass
\frac{\partial h}{\partial t} + \frac{\partial}{\partial x}\left(hu\right) = p
```
for the conservation of mass and, 
```{math}
:label: stv-momentum
\frac{\partial}{\partial t}\left(hu\right) + \frac{\partial}{\partial x}\left(hu^{2} +  g\frac{h^{2}}{2}\right) = -gh\frac{\partial z}{\partial x} - \tau
```
for the conservation of momentum. Where $h$ is the water depth, $u$ is the velocity, $p$ is the precipitation rate, $g$ is gravity, and $\tau$ is the fraction term. We will use the same friction model as Lisflood-FP ([Bates et al. 2010](https://doi.org/10.1016/j.jhydrol.2010.03.027)),
```{math}
:label: friction
\tau = \frac{n^{2} (hu)^{2}}{h^{7/3}}
```
where $n$ is Manning's roughness. In the diffusive wave assumption the $\partial/\partial x \left(hu^{2}\right)$ is assumed to be insignificant. Under this assumption the momentum balance reduces to,
```{math}
:label: stv-diff
\frac{\partial}{\partial t}\left(hu\right) + \frac{\partial}{\partial x}\left(g'\frac{h^{2}}{2}\right) = -g'h\frac{\partial z}{\partial x} - \tau
```
This reduced form of the shallow water equations can be solved for using semi-implicit or fully implicit schemes. The diffusive-wave approach ignores the advective terms and as such cannot capture the migration of a wave of water down the fluvial system. Furthermore, by not including the advective terms an instability in the solution can form as the flow accelerates down slope. One simplification to avoid such instabilities is to include a limit on the flux keeps the flux below a certain Froude number,
```{math}
F = \frac{u}{\sqrt{gh}}
```
such that the flux limiter is given by,
```{math}
q_{lim} = F_{lim} h \sqrt{gh}
```
where $F_{lim}$ is the maximum permissable Froude number for the flow and $q_{lim}$ is therefore the maximum water flux.

#### Microplastic transport

We will include microplastic as a grain fraction along with a selection of sediment grain fractions, where that input grainsize distribution, $F_{i}$, is distributed over an active layer thickness, $z_{a}$, to give a thickness of each grain size class, $g_{i}$ (see van der [Weil et al., 2007](#) for a description of active layers). Here $i \in \{0,N\}$ and $N$ is the number of grainsize classes. Subsequently the transport of sediment from the active layer is calculated from the empirical law developed by [Wilcock & Crowe (2003)](#) and the microplastic from a modified Meyer-Peter -- Muller law. For both we calulcate the shear stress on the bed, $\tau_{b}$, via the Manning-Strickler empirical law, such that,
```{math}
\tau_{b} = \rho_{w} C_{f} u^{2}
```
where,
```{math}
C_{f} = gn^{2}h^{-1/3}
```
To calculate the mircoplastic flux, where we assume a single grain size $D_{p}$, we use use the simple empirically derived model that above a shear stress, given by the dimensionless Shields number, the microplastic will be in motion. From the shear stress the dimensionless Shields numbner is given by,
```{math}
\theta = \frac{\tau_{b}}{\rho s g D_{p}}
```
The threshold Shields number for microplastic can be derived from laboratory experiemnts where the hiding effect that sediment grains have on the microplastic is estimated as[(Waldschläger and Schüttrumpf, 2019)](https://pubs.acs.org/doi/full/10.1021/acs.est.9b05394),
```{math}
\theta_{tp} = 0.5588 \theta_t \left(\frac{D_{p}}{D_{m}}\right)^{-0.503}
```
where $\theta_t$ is the threshold stress for the median grain size ($D_{m}$) of the distribution in the active layer. Subsequently if the Shields number exceeds the threshold the dimensionless flux per unit width is,
```{math}
q^{\star} = F_{p} C_{p} \left(\theta - \theta_{tp}\right)^{1.5}
```
where $F_{p}$ is the fraction of microplastic in the active layer and $C_{p}$ is a coefficient normally dervied from rating curves, giving a erosion flux of per unit width (in the 1D model units of m/sec) of,
```{math}
:label: erod-plastic
E_{p} = q^{\star}\left(sgD_{i}^3\right)^{1/2}
```
The loss of microplastic due to transport is then used to update the thickness of that active layer.

Once the microplastic has left the bed it is transported within the water column. It is assumed that there is a maximum transport capacity and if that limit is already reached no more microplastic can enter the water column even if the threshold Shields number is exceeded. The microplastic will subsequently fall out of suspension depending on its denisty. For simplicity we assume that the microplsatic has a constant fall velocty, and the deposition of the microplastic can be given from the rate at which the microplastic settles out of the water column. The mass balance for microplastic is therefore given by,
```{math}
:label: trans-plastic
\frac{\partial h_{susp}}{\partial t} + \frac{\partial}{\partial x}\left(h_{susp}u\right) = E_{p} - v_{drop} \left(\frac{h_{susp}}{h}\right)
```
Where $h_{susp}$ is the thickness microplsatic particles in suspension (which cannot exceed $C_{susp}h$ where $C_{susp}$ is the transport capacity of the water column), $q_{p}$ is the erosion rate from equation {eq}`erod-plastic`, and $v_{drop}$ is the fall velocity.

#### Sediment transport

AS mentioned above the transport of sediment from the active layer is calculated from the empirical law developed by [Wilcock & Crowe (2003)](#). The inclusion of this law follows the same methods as in Caesar-Lisflood, and ofr completness it is outlined here. From the shear stress the shear velocity is calculated as
```{math}
u_{\star} = nug^{1/2}h^{-1/6}
```
The rate of erosion is then given by,
```{math}
E_{s,i} = \frac{F_{i} u_{\star}^{3} W_{i}^{\star}}{sg}
```
where $s$ is the specific gravity of the sediment and $W_{i}^{\star}$ is a power law function that is dependent on the grain size and shear stress. A reference shear stress for each grain size fraction is defined empirically as,
```{math}
\tau_{r,i} = \rho g D_{m} \left(0.021 + 0.015e^{-20F_{s}}\right)  \left(\frac{D_{i}}{D_{m}}\right)^{ 0.67 / \left(1 + e^{\left(1.5 - (D_{i} / D_{m})\right)}\right)}
```
where $F_{s}$ is the fraction of sand within the grain size distribution, $D_{i}$ is the grain size, and $D_{m}$ is the median grain size in the active layer. Depending on the magnitude of the ratio of shear stress to reference shear stress, the function $W_{i}^{\star}$ is given by,
```{math}
\frac{\tau_{b}}{\tau_{r,i}} < 1.35, \ \ W_{i}^{\star} = 0.002 \left(\frac{\tau_{b}}{\tau_{r,i}}\right)^{7.5}
```
or,
```{math}
\frac{\tau_{b}}{\tau_{r,i}} \geq 1.35, \ \ W_{i}^{\star} = 14 \left(1 - 0.0894 \left(\frac{\tau_{b}}{\tau_{r,i}}\right)^{-0.5}\right)^{4.5}
```

The finest grain size is treated as a suspended particle, and its transport and deposition follows from equation {eq}`trans-plastic`. For the bed load the particles are routed down slope (see [Coulthard et al., 2001](#)).

#### Active layer and strata below

Following [Van de Weil et al. (2007)](http://dx.doi.org/10.1016/j.geomorph.2006.10.024) sediment layers are included, that is there is an active layer on the surface and then 9 "strata" layers below this. Each layer is defined to have an initial proportion of sediment grain sizes. Erosion and deposition act on the active layer, which is the layer at the surface. The inclusion of the notion of an active layer allows us to include microlastics within this layer only, such to potentially understand how surface deposits of microplastics get remobilised down the fluvial system. The active layer can be potentially significantly eroded or become excessively thick due to deposition. Therefore a rule is included to allow for material below to be added for the case of erosion, or for a new strata to be created in the case of deposition.

- Erosion: When the active layer is too thin the strata layer below is added to create a new active layer. The rule used is if the active layer thickness drops below 0.25 times its starting thickness then the layer below is added. This causes a change in the proportion of grains, as upon adding the strata layer, the distribution of grains includes that from the layer below. This is makes a mechanism that is similar to eroding away the cover of an alluvial bed and accessing the sediments below. Upon incorporation of the top strat layer a new bottom strata layer is added.

- Deposition: When the active layer is too thick a new strata layer is created. In this case if the thickness of the active layer exceeds 1.5 times its initial thickness a new strat layer is created. This new top strata layer keeps the distribution of grains with which it was created. Upon creation of this new top strata layer, the bottom strata is delted.

### Transport of microplastic and sediment in 1D

To test the numerical stability and applicability of the simple microplastic trasnport model coupled to the sediment transport model we first reduce the complexity of the problem and explore the limits and sensitivity of the model assumptions in one dimension. We solve for the flow of water using a implicit finite difference scheme and then update for the flow of sediment using a simple up-wind first order scheme (see Appendix). The shallow water equations are only valid for regions where there is water. We introduce a numerical condition to only solve for the flow of water if the precipitation adds enough water within the cell as to overcome a limit. Subsequently the erosion of microplastic and sediment is only applied if hte flow height of water is above a second numerical condition. These two condition are to avoid the application of the laws to unrealisitcally small flows of water.

```{figure} ./1dplots/initial_condition.png
---
name: 1d-initial
width: 80%
align: center
---
Initial elevation and distribrution of precipitation for the 1D model analysis. The model is initiated with increased elevation on the left to block water flow out of the left boundary. The gradient of the slope is varied from 0.01$^{\circ}$ to 5$^{\circ}$. Precipitation is also set to zero on the left hand side.
```

We initiate the model with a simple ramp-like topography that decreases from left to right, with a step increase in elevation at the right hand side to avoid the flow of water out of the right hand boundary ({numref}`1d-initial`). Precipitation is set up for all cells along the domain except for those that are within the region of high elevation on the right hand boundary ({numref}`1d-initial`). The model is initiated with a distribution of grain sizes, where there are nine classes, see Table {numref}`grains`. The active layer thickness is 0.5 m, and strata layers below are likewise 0.5 m thick. In the 1D model we will explore the sensitivity to model resolution, to the gradient of the initial slope, to the fall velocity of the microplastic and to grain size distribution.

```{list-table} Example grain sizes and distribution
:header-rows: 1
:name: grains

* - Name
  - $D_{i}$ (m)
  - $F_{i}$
* - microplastic
  - $0.0001$
  - $0.0001$
* - silt
  - $63.5\times 10^{-6}$
  - $0.3342$
* - very fine sand
  - $125\times 10^{-6}$
  - $0.2335$
* - fine sand
  - $250\times 10^{-6}$
  - $0.2206$
* - medium sand
  - $0.0005$
  - $0.1375$
* - coarse sand
  - $0.0001$
  - $0.0508$
* - granule
  - $0.002$
  - $0.0187$
* - pebble
  - $0.064$
  - $0.0047$
```

The boundary conditions for the water flow are of zero gradient in the flow height att he left hand side, however here there should be no water. On the right hand side we keep the hydraulic gradient fixed and equal to the topographic gradient.  

#### Steady state fluxes and model resolution

```{figure} ./1dplots/first-test.png
---
name: first-test
width: 80%
align: center
---
Model solution at steady state (after 10 hrs) for a slope of 0.1$^{\circ}$, precipitation rate of 0.01 m/hr, and sediment distribution as in {numref}`grains`: (a) Water depth, (b) Microplastic in transport, (c) Silt in transport, (d) Change in topography, (e) Proportion of microplastic in the active layer, (f) Active layer thickness.
```

```{note}
Fix axis size and figure labels
```

At steady state the water depth reaches a maximum of 0.03 m with the specific input precipitation rate and right hand boundary condition on the hydraulic gradient ({numref}`first-test`). With this small amount of water only a very small amount of microplastic is entrained into the water, with a larger proportion of silts. The erosion is of 0.2 m and the active layer is thinned by a corresponding amount within the model ({numref}`first-test`). Given that under these conditions there is greater erosion of sediment that microplastic, the proportion of microplsatic in the the active layer increases for this test scenario.

```{figure} ./1dplots/sensitivity-resolution.png
---
name: sens-res
width: 80%
align: center
---
Water and microplastic flux out of the 1D model for different model resolutions.
```

```{note}
Fix axis size, figure labels and legend
```

The flow of water down this simple slope is not trivial and the spatial resolution will impact the solution given. To explore numerically where the model is robust we first run a simple test with increasing spatial resolution, with 50, 200 and 500 cells to represent the 5000 m long domain, or eqivalent to 100, 25, and 10 m cell sizes. As the model resolution increases the flux of microplastic out of the 1D slope converges towards a similar quantity ({numref}`sens-res`). Therefore, we will take a resolution of 25 m cells in the following analysis of the 1D model.

#### Impact of slope

```{figure} ./1dplots/sensitivity-slope.png
---
name: sens-slope
width: 80%
align: center
---
Water and microplastic flux out of the 1D model for different model slopes. Note that the 2$^{\circ}$ model has the exact same flux as the 1$^{\circ}$ model.
```

```{note}
Fix axis size, figure labels and legend
```

To explore the impact of model slope on the solution we have run the same model for a 2 hr duration on slopes of 0.01$^{\circ}$, 0.1$^{\circ}$, 1$^{\circ}$ and 2$^{\circ}$. As the slope increases the velocity of the flow of water will increase, and hence the water flux. In the model, the water flux for a slope of 1$^{\circ}$ is identical to that of 2$^{\circ}$ ({numref}`sens-slope`). This is due to the flux limiter, which is necessary to avoid instabilities due to the diffusive-wave approximation. Hence there is a limitation to the solution for the overland flow of water for slopes steeper than 1$^{\circ}$, where the flux of microplastic and sediment will be underestimated due to the limit on the water velocity and hence shear stress on the bed. The result is that for a gradient greater than 1$^{\circ}$ we get identical results for the flux of microplastic ({numref}`sens-slope}).

#### Microplsatic fall velocity and location

I the final simple 1D model scenario we take advantage of the sediment layering and include microplastics only in a 1000 m long region within the centre of the model and microplastic is also only within the 0.5 m thick topmost active layer. We will model the impact of fast and slow fall velocities of the microplastic and the impact of the transport, erosion, and deposition of the microplsatic within the active layer below the source region. The model is run for 72 hours with an increased precipitation rate of 0.1 m/hr to speed up the water flux and capture the slower transport of microplsatics with a high fall velocity.

```{figure} ./1dplots/sensitivity-mp-location.png
:name: sens-loc
:align: center
:width: 80%

Water flux and microplastic flux for models with differnet fall velocities for the microplastic. The periodic jumps flux in jumps is due to the succesive erosion of the active layer into the strata below.
```

With the high erosion rates the active layer becomes sufficiently eroded that the lower strata become incorporated within the active layer. The incorporation of the lower strate initiates at the outlet of the 1D catchment and migrates up the system, resulting in period peaks in total sediment flux from the model ({numref}`sens-loc`). The peaks in sediment flux are due to the redistriubtion of the proportions of grain sizes after the addition of the lower strata layer, that means there is an sudden increase in the availability of more mobile grains. This periodic redistribution of grain sizes also impacts the median grain size and hence the hiding function for the erosion of microplastic from the bed, along with the proportion of microplastic found within the active layer if any microplastic deposition has occured down system. 

For different fall velocities the 1D model gives the expected result that for a slow fall velocity the microplsatic is rapidly exported from the model domain, while for a fast fall velocity the microplastic is removed more slowly and gets reworked within the active sedimentary layer. In the simple construct of this model, there is a periodic shift in the flux due to the erosion into new layers of sediment mixed with the active layer above. The simple model is an attempt to include the erosion into the sediments below the river bed as the cover is eroded away and the more mobile sediments below are exposed. In this 1D model the result is a regular pattern of increased flux, while for a more realisitc 2D model with a more stochastic forcing, the signal of the erosion into the layers of strata might become more obscure.

```{note}
Should find a way to plot the time evolution of the active layer changing
```

### Transport of microplsatic in 2D - incroporation into Casear-Lisflood

The aim of the 1D modelling was to test the sensitivity and behaviour of the addition of microplsatic transport to a model of sediment transport. The same code framework of the 1D transport model can be included within the landscape evolution model Caesar-Lisflood, in this case the C++ version Hail-Caesar. Microplastic is included as a grain clast that is transported within the water column as described in {eq}`trans-plastic`. However, the numerical scheme to include the transport is slightly different, as Caesar-Lisflood solves for the water flux within the cells and not at the cell boundaries, using a left-to-right and up-to-down sweeping algorithm, rather than resolving the flux at the cell interfaces. This has an impact on the sensitivity to spatial resolution. 

```{figure} ./2dplots/initial-DEM.png
---
name: 2D-topo
width: 80%
align: center
---
Initial synthetic topography to test the inclusion of a source of microplsatic. The gradeient along the V of the wedge is 1$^{\circ}$.
```

Building on the 1D model we first test the 2D model by including a source region of microplastic within a wedge-like topography. The initial condition is of a topography that is like a tilted and folded piece of paper, where the slope along the axis of the valley is 1$^{\circ}$ ({numref}`2D-topo`). The initial condition includes a slight surface roughness of a maximum amplitude of 0.1 m to help localise the flow of water. The active layer is set to be 0.1 m thick and the grain size distribution is as in {numref}`grains`. Precipitation rates are of 1 mm/hr or 0.1 mm/hr across the 5000 by 10000 m domain and fall velocities of 0.01 and 0.0001 m/sec are tested. Mircoplastics are set to be only in the topmost active layer and over a 1000 by 2000 m rectagle within the centre of the model domain.

#### Microplsatic transport in low flows

```{figure} ./2dplots/plastic-r0.100_v1e-4.png
---
name: r0.1_v1e-4
width: 80%
align: center
---
Water flow and plastic concentration in the active layer when the prectipitation rate is 0.1 mm/hr and the fall velocity of the microplastic is 0.0001 m/sec.
```

The overland flow of water collects within the valley as would be expected. For low precipitation rates the flow height is low and the channel width is only one cell wide {numref}`r0.1_v1e-4`. When the fall velocity is likewise low, there result is that the microplastic is stripped in the region where the water flow crosses the rectangular source zone. When the fall velocity is faster microplastic gets deposited within the channel {numref}`r0.1_v1e-2`. 

```{figure} ./2dplots/plastic-r0.100_v1e-2.png
---
name: r0.1_v1e-2
width: 80%
align: center
---
Water flow and plastic concentration in the active layer when the prectipitation rate is 0.1 mm/hr and the fall velocity of the microplastic is 0.01 m/sec.
```

```{figure} ./2dplots/plastic-flux-r0.100.png
---
name: flux_r0.1
width: 80%
align: center
---
Water flux microplastic flux and flux of silts from the model domain when the precipitation rate is 0.1 mm/hr.
```

In the two model cases with a low precipitation rate of 0.1 mm/hr the water flow is very low, and there is limited erosion of the active layer. The erosion is so low such that the active layer is not eroded sufficiently to require the addition of new material from the strata below. The microplsatic flux out from the model domain for a fall velocity of 0.01 m/sec is zero ({numref}`flux_r0.1`). When the fall velocity is 0.0001 m/sec the microplastic leaves the model domain in a time window of around 24 hrs ({numref}`flux_r0.1`).

#### Microplastic transport in high flows

```{figure} ./2dplots/plastic-r1.000_v1e-4.png
---
name: r1.0_v1e-4
width: 80%
align: center
---
Water flow and plastic concentration in the active layer when the prectipitation rate is 1 mm/hr and the fall velocity of the microplastic is 0.0001 m/sec.
```

```{figure} ./2dplots/plastic-r1.000_v1e-2.png
---
name: r1.0_v1e-2
width: 80%
align: center
---
Water flow and plastic concentration in the active layer when the prectipitation rate is 1 mm/hr and the fall velocity of the microplastic is 0.01 m/sec.
```

For the two models with a higher precipitation rate of 1 mm/yr the water depth is significantly higher, up to 0.3 m ({numref}`r1.0_v1e-4`}). The higher flux of water means the channel is wider and more microplastic gets stripped from the active layer. For a high fall velocity a significant quantity of microplastic gets deposited within the channel ({numref}`r1.0_v1e-2`}). 

```{figure} ./2dplots/active-layer-r1.000_v1e-2.png
---
name: act-lyr
width: 80%
align: center
---
Thickness of the active layer within the microplastic sorce region after 60 hours of model run time.
```

The increased water flux means that the active layer becomes significantly eroded, such that the strata below become incorporated within the active layer. Given that the incorporation of the lower strata is based on the criteria that the thickness of the active layer falls below one quarter of its initial thickness then the layer below is added. This means that cells will change in thickness of active layer and grain size distribution. Therefore, if there is a slight difference in thickness in adjacent cells causing once cell to meat the criteria while the neighbouring cell does not, there erosion rates will vary between the two cells due to the different grain size proportions. This will then cause a cascade in different local erosion rates, creating the apparently random nature of the active layer thickness of the channel bed at the end of the model run ({numref}`act-lyr`}.

```{figure} ./2dplots/plastic-flux-r1.000.png
---
name: flux_r1.0
width: 80%
align: center
---
Water flux microplastic flux and flux of silts from the model domain when the precipitation rate is 1 mm/hr.
```

This local change in active layer thickness and local grain size distribution has the effect of creating a sediment flux output that is unsteady through time ({numref}`flux_r1.0`). The movement in the sediment flux is not due to the water routing, but due to the shifts in grain size distribution as the active layer is adjusted locally due to erosion. Furthermore, the model would suggest that the addition of the small amount of microplastic within the source region will impact the release of silt from the model domain ({numref}`flux_r1.0`). This is due to the deposition of microplastic impacting the active layer thickness and so the outcome for the erosion of silt and the other sediment grains is not the for the two model scenarios.

### Landscape scale application - Têt catchment, France

#### Topography and hydrology

```{figure} ./2dplots/tet-vinca-catchment.png
---
name: dem
width: 80%
align: centre
---
Digital elevation model of the catchment of the Têt river, where the region upstream of the Vinça dam has been removed.
```

```{note}
Need to say where the DEM comes from
```

The Têt River is a typical coastal Mediterranean river with a drainage catchment area lower than 5000 km$^{2}$. The
coastal plain of the Têt watershed is characterized by agricultural activities, as well as the location of the largest city of the district (Perpignan, about 150,000 inhabitants). Two major dams exist in the Têt River basin and they lie upstream the densely populated coastal plains. The most downstream dam is at Vinça. This dam is used create a lake environment for summer activities. To simplify the application of the microplastic transport we will focus on the catchment below the dam, see {numref}`dem` as this region has low slopes, and therefore is more appropriate for the diffusive-wave approach of the overland flow model. Furthermore the alluvial plain contains the source region of microplastics due to the increased quantity of roads and population centres here.

```{figure} ./2dplots/tet-rainfall-zones.png
---
name: spatial-rain
width: 80%
align: centre
---
Zones used for each rainfall guage within the model catchment. For the daily rainfall data time series see {numref}`rainfall`
```

```{figure} ./2dplots/tet-vinca-rainfall.png
---
name: rainfall
width: 100%
align: centre
---
Daily rainfall data time series data used to force the model. For regions for which each timeseries is applied see {numref}`spatial-rain`.
```

The model is forced with a daily time series of rainfall from eight weather stations that are within or near the catchment ({numref}`spatial-rain` and {numref}`rainfall`). The zones for which each time series is applied is calculated using the nearest neighbour algorithm of the `scipy` python library. Given the low resolution of the rainfall data we chose to keep the spatial resolution at 200 m cell resolution. Noting that Caesar-Lisflood is resolution dependent ([Skinner & Coulthard, 2023](#), we think that a resolution of 200 m is appropriate for testing the applicability of the simple microplastic transport model given the broad assumptions on transport and the lack of detailed rainfall data at a high spatial and temporal resolution.

To avoid artificial damming of the river due to artefacts within the digital elevation model, we pre-process the DEM. We down-sample the DEM to 200 m cells using both the mean and minimum values. Using the mean and minimum values we run first the pit-filling algorithm `SinkFiller` from the `LandLab` library on both resampled DEMs, using the D4 routing to be consistent with the D4 nature of the Lisflood-FP algorithm implemented in Casear-Lisflood. Focusing on the minimum DEM we run the Lisflood-FP algorithm looped over two years of rainfall to obtain the river network. Regions where the water depth is greater than 0.05 m are then used to replace the mean value with the minimum value. Therefore, the pre-processed DEM consists of the mean value from the high resolution DEM on the hill slopes, and the minimum value within the channels.

```{figure} ./2dplots/tet-observations.png
---
name: obs
width: 80%
align: centre
---
Averaged catchment wide rainfall and observations of water flux, suspended sediment, and microplastic concentration at ? ([Constant et al., 2020](#))
```

At the outlet to the Têt there was punctual measurements of microplastic concentrations in 2016 ([Constant et al., 2020](#)). Combined with the gauging station there are therefore observations for water flux, suspended sediment load, and microplastic ({numref}`obs`}. We first calibrate the hydrological model against the observations of water flux. The two key parameters to which the model is sensitive are the assumed quantity of rainfall that passes through the evapotranspiration into run-off, and the storage of water within the subsurface via the `TOPMODEL` parameter $m$. For evapotranspiration we make the simplified assumption that it is constant in time and simple reduce the precipitation by a factor of 80, 60 or 40%. `TOPMODEL` is a simple set of logarithmic functions that approximate the hydrographic response to precipitation to give the characteristic recession curve within a river after precipitation input ([Beven](#)). The parameter $m$ controls the hydrographic response, where smaller $m$ leads to a faster return in the run-off to the steady state after a rain-fall input. We test the range of $m$=0.006 to 0.014.

```{figure} ./2dplots/tet-fit.png
---
name: nse-fit
width: 100%
align: centre
---
Nash-Sutcliff model efficiency for the model for different assumptions on rainfall loss due to evapotranspiration and subsurface storage via the `TOPMODEL` parameter $m$ that controls the peak and duration of the recession curve.
```

By comparing the rainfall time series averaged over the catchment below the Vinca dam and the water flux observed at Perpignan, we can see that out flows of the dam during the summer months impact the observed water flux, exemplified by the large flow event in mid May, 2016 ({numref}`obs`). To compare the model water flux with the observations, we therefore focus on the spring and autumn months, from 15/2/2016 to 1/5/2016 and from 1/9/2016 to 1/11/2016. The quality of model fit is estimated using the Nash-Sutcliff model efficiency, where if the fit is negative, then the model is no better than the time averaged mean, while good fits are closer to 1. We find that most models do not give a good fit, however if we assume that 40% of precipitation effectively reaches the surface, and a `TOPMODEL` $m$ parameter of 0.008 to 0.010 then the NSE value is 0.46 to 0.47.

#### Microplastic source and concentrations

Microplastics primarily enter river systems through wastewater and the dumping of waste. The quantity of microplastics entering the rivers can be significantly reduced through wastewater treatment, however microplastics related for fabrics are still transported into the rivers (Woodward et al., 2021). From looking at samples from riverbank deposits, it was found that in the River Tame, UK, microplastic is sourced from both treated wastewater and more importantly untreated waste. Two sources are highlighted (Woodward et al., 2021): (1) continuous transport at low concentrations of synthetic fibres from treated wastewater effluent; and (2) episodic flood-driven transport of the full microplastic assemblage entrained from contaminated channel beds. The evidence from this catchment would suggest that there are point sources of continuous microplastic from urban treatment works and factories. Flood waters will however source microplastics that have become deposited within the river catchment and hence there are events where microplastic is sourced catchment wide. In a similar study on the Brisbane River, Australia, it was found that the concentration of microplastic deposited at the riverbank did not vary spatially along with land use, however the types of plastic found vary (He et al., 2020). In rural areas microplastic deposition was dominated by polyethylene (He et al., 2020).

In water treatment station, the dense microplastics are typically removed through settling and remain in the sludge. Microplastics within fabrics are significantly harder to remove, hence they seep into the rivers along with the treated water. The sludge is however processed and used in many countries as a fertiliser. This raises the potential that microplastic enters rivers from runoff withing agriculture land (Nizzetto & Bussi et al., 2016), and might explain the lack of variability in microplastic concentrations in the Brisbane River. Atmospheric falls could also act as a source of microplastic in soil and along catchment slopes. Regardless, microplastics are ubiquitous within the environment, and with the current lack of continuous monitoring within any fluvial system there is current estimates on the sources of microplastic are extrapolated from a handful of measurements.

```{figure} ./2dplots/pop-density.png
---
name: pop
width: 80%
align: centre
---
Population density of the Tet catchment below the Vinca dam
```

To estimate the source of microplastics within the catchment we make the simple assumption that microplastics in the active layer of hte model are related to the population density. Using the INSEE Filosofi population database averaged of a 200 m raster ([Filosofi](https://www.insee.fr/fr/statistiques/6215168?sommaire=6215217)), we created a map of the population density ({numref}`pop`). We then relate the population density to a proportion of microplastic within the active layer, where if the the population density is greater than 2 persons per square meter then the proportion of microplsatics in the surface is assumed to be 0.0001, while if the population density is between 0.5 and 2 persons per square meter, the proportion of microplastics is $1\times 10^{-5}$. For all other regions there is assumed to be no microplastic. There is very little information on the quantity of microplastic in surface soils within rural France. We base the proportions used on the mass concentrations of microplastics observed in dredge sample from the Aa River in northern France, where contaminated soils contained up to 0.1 g/kg of microplastic ([Constant et al., 2021](https://doi.org/10.1021/acs.est.0c08386)). This observations is likely at the top end of the possible contaminated soils, so we create maps with what we will term *high*, *mid* and *low* contamination (see {numref}`mp-contam`).

```{list-table} Range of tested microplastic concentrations in active layer
:header-rows: 1
:name: mp-contam

* - Population density range (persons/m$^{2}$)
  - *high* MP concetration
  - *mid* MP concetration
  - *low* MP concetration
* - $> 2$
  - $10^{-4}$
  - $10^{-5}$
  - $10^{-6}$
* - $> 0.5$
  - $10^{-5}$
  - $10^{-6}$
  - $10^{-7}$

```

The model is run assuming the active layer has a thickness of 0.5 m and it is only the initial active layer that contains microplastics within the regions defined by their population density. The microplastic then gets transported by the overland flow and settles assuming a fall velocity of $10^{-4}$ and $10^{-6}$ m/sec. We loop the model over duration of the rainfall series from October 2015 to April 2018 ({numref}`rainfall`) to have model wind up prior to a model with a more connected fluvial network. The punctual observations of microplastic concentrations can be used to attempt to verify if the model is capable of generating sensible values for the quantity of microplastic in transport. We explore both the range of concentrations of microplastic in the active layer related to the population density and two fall velocities. Given the observations are punctual and do not caputre peak flow conditions we will not look at the time series, but instead look at the relationships modelled and observed between water flux and microplastic concentrations ({numref}`concfit` and {numref}`vdropfit`).

```{figure} ./2dplots/tet-MP-concfit.png  
---
name: concfit
width: 80%
align: centre
---
Comparison of observed and modelled microplastic concentrations at the outlet of the Tet catchment for different intial concentrations of microplastics ({numref}`mp-contam`) assuming a fall velocity of $10^{-4}$ m/sec.
```

From a comparison of the model microplastic fluxes and the observations it is clear that the *high* initial contamination of microplastics generates more microplastic in suspension that the observations ({numref}`concfit`, light blue). The *low* contimination model generates too litle microplastic ({numref}`concfit`, salmon red), while the *mid* contamination generates a trend in microplastic concentrations that is coincodent with the observations ({numref}`concfit`, light green). The implies that under the assumptions of this model, at a resolution of 200 m cells the microplastic volume concentrations related to populated areas could be in the range of $10^{-6}$ to $10^{-5}$. The fall velocity of the microplastics will however impact the flux out of the system. If the fall velocity is of the order of $10^{-6}$ m/sec the microplastic concentration at the outlet increases by an order of magnitude ({numref}`vdropfit`). This change is however within the range of observed concetrations. This could imply that due to alteration, floculation and weathering the fall valocity of the microplastics could be in the range of 10^{-4}$ to $10^{-6}$ m/sec.

```{figure} ./2dplots/tet-MP-vdropfit.png
---
name: vdropfit
width: 80%
align: centre
---
Comparison of observed and modelled microplastic concentrations at the outlet of the Tet catchment for different fall velocities assuming an intial concentrations of microplastics that is in the *mid* range, where for a population density greater than 2 person/m$^{2}$ the volume concentration is $10^{-5}$ ({numref}`mp-contam`).
```

If we assume that the *mid* model of initial microplastic contamination and a fall velocity of $10^{-4}$ m/sec is representative of the Têt catchment discounting the area above the Vinça dam, then we find that microplasti cbecomes entrained and deposited within the Têt fluvial system ({numref}`tet-mp`). As with the simple test on the simple rectangular catchment, we can see that the model creates a channel with a distribution of regions of low and high active layer thickness as this layer becomes eroded and is unified with the strata below ({numref}`tet-mp`a). The water height within the alluvial plain is of the order of meters, which is consisent with the Têt river, given the low resolution of the model ({numref}`tet-mp`b). Within this channel, after a model evolution of 5 years the micropalstic has started to accumulate within reaches of the Têt, particularly to the west of the densly populated region of Perpignan and at the outlet ({numref}`tet-mp`c).

```{figure} ./2dplots/tet-mid-200-r40-v1e-4-t1815.png
---
name: tet-mp
width: 80%
align: centre
---
Change in the active layer, water depth, and concentration of microplastics for the *best fit* model scenario.
```

### Discussion



