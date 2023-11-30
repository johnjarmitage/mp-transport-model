---
# File metadata may be provided as frontmatter YAML
title: A numerical model of microplastic transport for fluvial systems in the land-sea continuum
short_title: Microplastic transport in fluvial systems
description: Article describing a method to model the transport of microplastic along a fluvial system
authors:
  - name: John J. Armitage
    corresponding: true
    equal_contributor: false
    email: john-joseph.armitage@ifpen.fr
    roles:
      - Conceptualization
      - Methodology
      - Software
      - Writing - original draft
    affiliations:
      - id: IFPEN
        institution: IFP Energies Nouvelles
        address: 1 et 4 avenue de Bois-Préau
        city: Rueil-Malmaison
        country: France
        postal_code: 92852
  - name: Sébastien Rohais
    corresponding: false
    equal_contributor: false
    email: sebastien.rohais@ifpen.fr
    affiliation: IFPEN
    roles:
      - Conceptualization
      - Project administration
      - Writing - original draft
date: 2023-10-20
keywords:
  - microplastic
  - environmental pollution
  - sediment transport
  - landscape evolution model
bibliography:
  - article.bib
tags:
  - Shallow water equations
  - Sediment transport
exports:
  - format: docx
    output: exports/mp-transport-model.docx
  - format: pdf
    template: /work/armitagj/code/myst-templates/springer
    output: exports/mp-transport-model.pdf 
    formatting: onecolumn
    referee: true
    line_numbers: true
    numbered_referencing: false
    pdf_latex: true
    reference_style: chicago
---

<!---
  - format: pdf
    template: eartharxiv
    output: exports/pre-print.pdf 
    two_column: false
    line_numbers: true
  - format: pdf
    template: /work/armitagj/code/myst-templates/springer
    output: exports/mp-transport-model.pdf 
    formatting: onecolumn
    referee: true
    line_numbers: true
    numbered_referencing: false
    pdf_latex: true
    reference_style: chicago
--->

+++{"part": "abstract"}
Rivers are the primary pathway of microplastic pollution from source to the eventual sink in the marine environment. However, like sediments, microplastic will become trapped within the fluvial system as it makes its way from source-to-sink. There is therefore the potential that rivers are an important reservoir of microplastic pollution globally. To explore the transport of microplastic through the fluvial system we develop a reduced complexity model of microplastic erosion, transport, and deposition that builds on methods developed for the transport of sediment. We apply this model to the river Têt, where there has been punctual monitoring of the flux of microplastic at the outlet. We find that the reduced complexity model captures the observed quantity of microplastic under reasonable assumptions of the relationship between microplastic sources and population density. The model that best matches observed fluxes of microplastic at the outlet of the Têt river requires between 1 and 10 ppm volume concentration of microplasitc per 200$\times$200 m in the top half a meter of soil. This concentration of microplastic then travels within the river network with a settling velocity of between 10<sup>-4</sup> and 10<sup>-6</sup> m/sec. The model results imply that a large proportion of microplastic will become entrained within the sediments along the fluvial system. This model is a first step in assessing where to sample for microplastic pollution within river networks and points to regions susceptible to microplastic pollution.
+++

# Introduction

It is not controversial to state that there is an increasing awareness of the threat of plastic pollution on the natural environment and public health. Plastics are a highly durable material and cheap to produce. They are used in nearly all industrial processes and have entered the atmosphere, hydrosphere, and solid Earth. Plastics once used are either recycled, incinerated, or dumped. It is estimated that roughly 9% of plastics created are recycled, and about 12% are incinerated [@geyer-etal-2017]. What is left is discarded either in landfill, in open pits as miss-managed waste, or enters the environment through waste water treatment. Estimates of total plastic show an exponential growth in production. In 2015 it was estimated that 407 Mt of primary plastic (virgin plastic created from raw materials) entered into a use phase, while 302 Mt left the use-phase [@geyer-etal-2017]. Of that stock of plastic that is estimated to have left use, roughly 80% will enter landfill or be dumped in open pits. Plastic that is not contained within managed landfill is termed mismanaged plastic waste (MPW) and it is estimated that in 2015 there was between 65 and 99 Mt of MPW [@lebreton-2019]. Of this, estimates are that somewhere between 0.8 and 4 Mt per year enters the ocean via the river network [@schmidt-etal-2017; @lebreton-2019; @meijer-etal-2021]. The estimates of plastic waste that enters the ocean differ on the assumptions for the spatial distribution of MPW and the impact of climate on river run-off on the delivery of plastic to the oceans. This means that the importance of small river catchments versus large rivers is still uncertain. This uncertainty is demonstrated by, for example, a local study of microplastic contamination within the Mersey and Irwel rivers in England. These two small rivers were found to contain roughly half a million particles of microplastic per meter, which makes them one of the most polluted rivers globally [@hurley-etal-2018]. This watershed however does not suffer from open tipping of plastic, and there is no strong evidence for mismanaged water treatment. Therefore, it is possible that either significantly more plastic than previously thought is stored within rivers [@emmerik-etal-2022], or significantly more plastic than estimated enters the oceans.

The challenge is that currently there is not sufficient data to distinguish between the quantity of river and ocean microplastic storage, and furthermore current models do not take into account the transport and deposition of micropalstic particles down system. The box model developed by @sonke-etal-2022 for a global mass balance of microplastic treats river transport only as an input to the ocean sinks and reaches the conclusion that a considerable quantity of microplastics accumulate in the deep ocean. On a global scale it has been observed that the quantity of microplastic that enters the rivers is related to the population density, yet the focus has been on estimates for the flux of microplastic as suspended load [e.g. @weiss-etal-2021]. In this study we wish to model the transport of microplastic within the rivers to capture the potential for micropalstic to become deposited within the fluvial system en route to the ocean basin. The goal is to develop a tool that may help understand where temporary or permanent storage of microplastics will occur within river systems.

# Brief review of existing models for landscape-scale transport of microplastics

There is a surprising lack of numerical models for the transport of microplastics. The process-based model `INCA-Contaminants` was developed with the aim to model the transport of microplastics [@nizzetto-etal-2016]. This model divides the river catchment into reaches and within each reach assumes sediment is transported along the reach with the water flow and with a fall velocity. `INCA-contaminants` was used to explore the potential microplastic transport within the River Thames, UK. Unfortunately, there are no observations for microplastics within the water column or within the sediments for any comparison between the model prediction and the natural river system. In effect the model treats microplastic as a particularly light sediment, yet this assumption has not been tested. The model `INCA-contaminates` is a slight modification on `INCA-seds` [@lazar-etal-2010]. These models solve for the transport of particles by reducing the spatial dimensions to a one dimension and splitting the catchment into reaches and modelling the various input pathways to each reach [@lazar-etal-2010]. The reach approach has also been used in a second model that captures the advection and dispersion of microplastic down system [@dearbeloa-2023], rather than the more simple settling approach of `INCA-seds`. Both models reduce the two dimensional catchment topology to a series of one dimensional ordinary differential equations and as such the lateral spatial variability in sediment or microplastic deposition cannot be captured. Instead the focus is on capturing the flux of microplastic as it enters at point locations and leaves the catchment at a particular gauging station or other point of interest [@dearbeloa-2023; @nizzetto-etal-2016]. This means the long-term storage of microplastic within the floodplains or other three-dimensional natural barriers cannot be captured.

A different approach would be to model the full problem of the flow of surface water, as is taken to model particles of car tires [@unice-etal-2019]. These particles are assumed to travel with runoff and hence a hydrological model, `DELFT3D-WAQ`, is used to model the freshwater flow. Based on the flow pathways and estimates of removal of the particles from these flow pathways due to water treatment and deposition within the sediment, the amount of tire particles that remain in the environment can be estimated. This sort of model is essentially a post-processing of a hydrological model that solves for the shallow water equations within the terrestrial environment. As such it might not be appropriate for exploring how microplastics get transported, stored, and remobilized within the fluvial environment.

In estuaries, the transport of microplastic has been modelled at a similar level of complexity as the tire particles discussed above. With a focus on the Chesapeake Bay, the transport of micropalstic treated as a trace particle in the regional ocean circulation model applied to the estuary was used to predict where microplastics would accumulate in the water column [@lopez-etal-2021]. This approach however excludes the potential for deposition of the microplastic and is limited to regions where there are large bodies of water, such as lakes and estuaries. As such it is difficult to adapt this to microplastic transport within rivers, where the overland flow of water cannot be captured with the same assumptions behind oceanographic circulation models.

As a compromise between the reduced dimension model and the full Navier-Stokes problem, the transport of microplastics could be captured using reduced complexity models as applied to tracking contaminated sediment or to study the source-to-sink pathway of sediment released from earthquakes [@coulthard-2003; @xie-etal-2022]. These models use the empirical transport equations for sediments developed by [@wilcock-2003] to link the water flux to sediment flux to capture the transport of multiple grain sizes down system (`CAESAR-Lisflood`; @coulthard-etal-2013). Subsequently, certain grains are tracked as they flow through the system. In these examples, the landscape evolution model is used to track sediments and not microplastics, however there is the potential in this approach for tracking microplastic as it migrates down the fluvial system. The advantage is that the spatial distribution of deposition can be captured, in particular deposition in floodplains. For example, from modelling lead contamination in sediments it was found that avulsions within the depositional environment lead to a significant reworking of previous deposits and a release of contaminants into the watershed [@coulthard-2003]. Given that microplastics are found within riverbank deposits, avulsions would have a similar impact on microplastic contamination.

# Modelling microplastic as a sediment

## Water flux

To include microplastics within a sediment transport model we build upon the framework of `CAESAR-Lisflood` [@coulthard-etal-2013], where the flow of water is solved for assuming the diffusive-wave approximation to the shallow water equations, and that the microplastic is eroded from the bed above a critical threshold. The full Saint-Venant shallow water equations can be written as,
```{math}
:label: stv-mass
\frac{\partial h}{\partial t} + \frac{\partial}{\partial x}\left(hu\right) = p
```
for the conservation of mass and, 
```{math}
:label: stv-momentum
\frac{\partial}{\partial t}\left(hu\right) + \frac{\partial}{\partial x}\left(hu^{2} +  g\frac{h^{2}}{2}\right) = -gh\frac{\partial z}{\partial x} - \tau
```
for the conservation of momentum. Where $h$ is the water depth, $u$ is the velocity, $p$ is the precipitation rate, $g$ is gravity, and $\tau$ is the friction term. We will use the same friction model as `Lisflood-FP` [@bates-etal-2010],
```{math}
:label: friction
\tau = \frac{n^{2} (hu)^{2}}{h^{7/3}}
```
where $n$ is Manning's roughness. In the diffusive wave assumption the $\partial/\partial x \left(hu^{2}\right)$ is assumed to be insignificant. Under this assumption the momentum balance reduces to,
```{math}
:label: stv-diff
\frac{\partial}{\partial t}\left(hu\right) + \frac{\partial}{\partial x}\left(g\frac{h^{2}}{2}\right) = -gh\frac{\partial z}{\partial x} - \tau
```
This reduced form of the shallow water equations can be solved for using semi-implicit or fully implicit schemes. The diffusive-wave approach ignores the advective terms and as such cannot capture the migration of a wave of water down the fluvial system. Furthermore, by not including the advective terms an instability in the solution can form as the flow accelerates down slope. One simplification to avoid such instabilities is to include a limit on the flux to keep the flux below a certain Froude number [@coulthard-etal-2013],
```{math}
F = \frac{u}{\sqrt{gh}}
```
such that the flux limiter is given by,
```{math}
q_{lim} = F_{lim} h \sqrt{gh}
```
where $F_{lim}$ is the maximum permissible Froude number for the flow and $q_{lim}$ is therefore the maximum water flux.

## Microplastic transport

We will include microplastic as a grain fraction along with a selection of sediment grain fractions, where the input grain size distribution, $F_{i}$, is distributed over an active layer thickness, $z_{a}$, to give a thickness of each grain size class, $g_{i}$ [@vandewiel-etal-2007]. Here $i \in \{0,N\}$ and $N$ is the number of grain size classes. Subsequently the transport of sediment from the active layer is calculated from the empirical law developed by @wilcock-2003 and the microplastic from a modified Meyer-Peter and Müller law. For both we calculate the shear stress on the bed, $\tau_{b}$, via the Manning-Strickler empirical law, such that,
```{math}
\tau_{b} = \rho_{w} C_{f} u^{2}
```
where,
```{math}
C_{f} = gn^{2}h^{-1/3}
```
To calculate the mircoplastic flux, where we assume a single grain size $D_{p}$, we use use the simple empirically derived model that above a shear stress, given by the dimensionless Shields number, the microplastic will be in motion. From the shear stress the dimensionless Shields number is given by,
```{math}
\theta = \frac{\tau_{b}}{\rho s g D_{p}}
```
The threshold Shields number for microplastic can be derived from laboratory experiments where the hiding effect that sediment grains have on the microplastic is estimated as [see @waldschlager-2019],
```{math}
\theta_{tp} = 0.5588 \theta_t \left(\frac{D_{p}}{D_{m}}\right)^{-0.503}
```
where $\theta_t$ is the threshold Shields number for the median grain size ($D_{m}$) of the distribution of grain sizes in the active layer. Subsequently if the Shields number exceeds the threshold, the dimensionless flux per unit width is,
```{math}
:label: MPM
q^{\star} = F_{p} C_{p} \left(\theta - \theta_{tp}\right)^{1.5}
```
where $F_{p}$ is the fraction of microplastic in the active layer and $C_{p}$ is a coefficient normally derived from rating curves. For sediments $C_{p}$ is estimated to be equal to 3.97 [e.g. @wong-2006; @huang-2010]. A similarly large data set of catchment-scale observations does not exist for microplastic, but from laboratory experiments $C_{p}$ can be estimated to be of the order of 2.4 ({numref}`MPM-plastic`). The precise value of $C_{p}$ can be tuned for landscape scale model runs. From the Shields number the erosion flux of per unit width (in the 1D model units of m/sec) is,
```{math}
:label: erod-plastic
E_{p} = q^{\star}\left(sgD_{i}^3\right)^{1/2}
```
The loss of microplastic due to transport is then used to update the thickness of the active layer.

```{figure} ./1dplots/MPM-plastic.png
---
name: MPM-plastic
width: 45%
align: center
---
Relationship between the dimensionles flux $q^{\star}$ and Shields number $\theta$ for the laboratory experiments of the transport of plastic beads down an inclined slope [@berzi-2013]. The gradient of the linear regresion is 1.92 between $\left(q^{\star}\right)^{2/3}$ and $\theta$, suggesting $C_{p}\approx2.4$ in equation {eq}`MPM`.
```

Once the microplastic has left the bed it is transported within the water column. It is assumed that there is a maximum transport capacity and if that limit is already reached no more microplastic can enter the water column, even if the threshold Shields number is exceeded. The microplastic will subsequently fall out of suspension depending on its density. For simplicity we assume that the microplastic has a constant fall velocity, and the deposition of the microplastic can be given from the rate at which the microplastic settles out of the water column. The mass balance for microplastic is therefore given by,
```{math}
:label: trans-plastic
\frac{\partial h_{susp}}{\partial t} + \frac{\partial}{\partial x}\left(h_{susp}u\right) = E_{p} - v_{drop} \left(\frac{h_{susp}}{h}\right)
```
Where $h_{susp}$ is the thickness microplastic particles in suspension (which cannot exceed $C_{susp}h$ where $C_{susp}$ is the transport capacity of the water column), $q_{p}$ is the erosion rate from equation {eq}`erod-plastic`, and $v_{drop}$ is the fall velocity.

## Sediment transport

As mentioned above the transport of sediment from the active layer is calculated from the empirical law developed by @wilcock-2003. The inclusion of this law follows the same methods as in `CAESAR-Lisflood`, and for completeness it is outlined here. From the shear stress the shear velocity is calculated as
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

The finest grain size is treated as a suspended particle, and its transport and deposition follows from equation {eq}`trans-plastic`. For the bed load the particles are routed down slope [see @coulthard-etal-2013].

## Active layer and strata below

Following @vandewiel-etal-2007 sediment layers are included, that is there is an active layer on the surface and then 9 *strata* layers below this. Each layer is defined to have an initial proportion of sediment grain sizes. Erosion and deposition act on the active layer, which is the layer at the surface. The inclusion of the notion of an active layer allows us to include microplastics within this layer only. This will allow the modelling of how the microplastics in a contaminated layer get remobilized down the fluvial system. The active layer can be potentially significantly eroded or become excessively thick due to deposition. Therefore a rule is included to allow for material below to be added for the case of erosion, or for a new strata to be created in the case of deposition.

- Erosion: When the active layer is too thin the strata layer below is added to create a new active layer. The rule used is if the active layer thickness drops below 0.25 times its starting thickness then the layer below is added. This causes a change in the proportion of grains, as upon adding the strata layer, the distribution of grains includes that from the layer below. This is makes a mechanism that is similar to eroding away the cover of an alluvial bed and accessing the sediments below. Upon incorporation of the top strata layer a new bottom strata layer is added.

- Deposition: When the active layer is too thick a new strata layer is created. In this case if the thickness of the active layer exceeds 1.5 times its initial thickness a new strata layer is created. This new top strata layer keeps the distribution of grains with which it was created. Upon creation of this new top strata layer, the bottom strata is deleted.

# Transport of microplastic and sediment in 1D

To test the numerical stability and applicability of the simple microplastic transport model coupled to the sediment transport model we first reduce the complexity of the problem and explore the limits and sensitivity of the model assumptions in one dimension. We solve for the flow of water using a implicit finite difference scheme and then update for the flow of sediment using a simple up-wind first order scheme. The shallow water equations are only valid for regions where there is water. We introduce a numerical condition to only solve for the flow of water if the precipitation adds enough water within the cell as to overcome a limit, set at 10<sup>-5</sup> m. Subsequently the erosion of microplastic and sediment is only applied if the flow height of water is above 1 cm, as below 1 cm the flow is minimal. These two condition are to avoid the application of the laws to unrealistically small flows of water.

```{figure} ./1dplots/initial_condition.png
---
name: 1d-initial
width: 45%
align: center
---
Initial elevation and distribrution of precipitation for the 1D model. (a) Elevation: the model is initiated with increased elevation on the left to block water flow out of the left boundary. The gradient of the slope is varied from 0.01$^{\circ}$ to 5$^{\circ}$. (b) Precipitation rate: precipitation is constant in time but varies spatialy to avoid flow into the topographic barrier. This is to always have flow in the right hand direction.
```

We initiate the model with a simple ramp-like topography that decreases from left to right, with a step increase in elevation at the right left side to avoid the flow of water out of the left hand boundary ({numref}`1d-initial`). Precipitation is set up for all cells along the domain except for those that are within the region of high elevation on the right hand boundary ({numref}`1d-initial`). The model is initiated with a distribution of grain sizes, where there are nine classes, see {numref}`grains`. The active layer thickness is 0.5 m, and strata layers below are likewise 0.5 m thick, unless otherwise stated. In the 1D model we will explore the sensitivity to model resolution, to the gradient of the initial slope, to the fall velocity of the microplastic, and to the spatial distribution of microplastic.

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
  - $0.001$
  - $0.0508$
* - granule
  - $0.002$
  - $0.0187$
* - pebble
  - $0.064$
  - $0.0047$
```

The boundary conditions for the water flow are of zero gradient in the flow height on the left hand side, here there should be no water. On the right hand side we keep the hydraulic gradient fixed and equal to the topographic gradient.  

## Steady state fluxes and model resolution

```{figure} ./1dplots/first-test.png
---
name: first-test
width: 45%
align: center
---
Model solution at steady state (after 10 hrs) for a slope of 0.1$^{\circ}$, precipitation rate of 0.01 m/sec, and sediment distribution as in {numref}`grains`: (a) Water depth, (b) volume concentration of microplastic in transport, (c) volume concentration of silt in transport, (d) change in topography, (e) volume concentration of microplastic in the active layer and, (f) active layer thickness.
```

In the first set of model runs we have increased the active layer thickness to 1 m, to avoid that the active layer is completely eroded away for the long model duration that is required to achieve steady state. At steady state the water depth reaches a maximum of 0.03 m with a precipitation rate of 0.01 m/sec and right hand boundary condition of a hydraulic gradient of 0.1$^{\circ}$ ({numref}`first-test`). With this small amount of water only a very small amount of microplastic is entrained into the water, with a larger proportion of silts. The erosion is of 0.2 m and the active layer is thinned by a corresponding amount within the model ({numref}`first-test`). Given that under these conditions there is greater erosion of sediment than microplastic, the volume concentration of microplastic in the the active layer increases for this test scenario.

```{figure} ./1dplots/sensitivity-resolution.png
---
name: sens-res
width: 80%
align: center
---
Water and microplastic flux out of the 1D model for different model resolutions. (a) Water flux and (b) microplastic (MP) flux for a resolution of 50, 200 or 500 cells, corresponding to a length of 100, 25 and 10 m cell length.
```

The flow of water down this simple slope is not trivial and the spatial resolution will impact the solution given. To explore numerically where the model is robust we first run a simple test with increasing spatial resolution, with 50, 200 and 500 cells to represent the 5000 m long domain, or equivalent to 100, 25, and 10 m cell sizes. As the model resolution increases the flux of microplastic out of the 1D slope converges towards a similar quantity ({numref}`sens-res`). Therefore, we will take a resolution of 25 m cells in the following analysis of the 1D model.

## Impact of slope

```{figure} ./1dplots/sensitivity-slope.png
---
name: sens-slope
width: 80%
align: center
---
Water and microplastic flux out of the 1D model for different model slopes. (a) Water flux and (b) microplastic (MP) flux, where the colours are for different topographic slopes. Note that the 2$^{\circ}$ model has the exact same flux as the 1$^{\circ}$ model.
```

To explore the impact of model slope on the solution we have run the same model for a 2 hr duration on slopes of 0.01$^{\circ}$, 0.1$^{\circ}$, 1$^{\circ}$ and 2$^{\circ}$. As the slope increases the velocity of the flow of water will increase, and hence the water flux. In the model, the water flux for a slope of 1$^{\circ}$ is identical to that of 2$^{\circ}$ ({numref}`sens-slope`). This is due to the flux limiter, which is necessary to avoid instabilities due to the diffusive-wave approximation. Hence there is a limitation to the solution for the overland flow of water for slopes steeper than 1$^{\circ}$, where the flux of microplastic and sediment will be underestimated due to the limit on the water velocity and hence shear stress on the bed. The result is that for a gradient greater than 1$^{\circ}$ we get identical results for the flux of microplastic ({numref}`sens-slope`).

## Microplastic fall velocity and location

In the final simple 1D model scenario we take advantage of the sediment layering and include microplastics only in a 1000 m long region within the centre of the model. We will model the impact of fast and slow fall velocities of the microplastic and the impact of the transport, erosion, and deposition of the microplastic within the active layer below the source region. The model is run for 72 hours with an increased precipitation rate of 0.1 m/sec to speed up the water flux and capture the slower transport of microplastics with a high fall velocity.

```{figure} ./1dplots/active-layer-erosion.png
:name: active-layer
:align: center
:width: 80%

Plot of evoloution of model surface while the active layer becomes eroeded and then encorporates the strata below. (a) Suspended volume concentration of microplastic, (b) elevation difference from the initial condition, (c) thickness of the active layer, and (d) volume concentration of miroplastic that remains within the active layer.
```

With the high erosion rates the active layer becomes sufficiently eroded that the lower strata become incorporated within the active layer. In {numref}`active-layer` the behaviour of the model as the active layer becomes eroded into is plotted. Early in the model evolution, at the right hand boundary there is sufficient erosion that the active layer crosses the lower threshold and the layer below is added. This has the effect of changing the grain size distribution and therefore the rate of erosion of the newly modified layer. The finest grains can be eroded more rapidly, and the addition of the strata below increases the proportion of fine grains, increasing erosion rates. This knick-point migrates upstream, with a speed that reduces with distance up-stream. The result is a cascade of upstream migrating knick-points ({numref}`active-layer`), where by as the model evolves the number of knick-points increase. The change in grain size distribution down system also impacts the amount of microplastic put into suspension, creating jumps in the concentration of microplastic within the water column and likewise the flux of microplastic and sediment at the right hand boundary of the model ({numref}`sens-loc`).

```{figure} ./1dplots/sensitivity-mp-location.png
:name: sens-loc
:align: center
:width: 80%

Water flux and microplastic flux for models with differnet fall velocities for the microplastic. (a) Water flux and (b) micropastic (MP) flux for a fall velocity of 10<sup>-2</sup> to 10<sup>-4</sup> m/sec. The periodic jumps flux in jumps is due to the succesive erosion of the active layer into the strata below (see {numref}`active-layer`).
```

For different fall velocities the 1D model gives the expected result that for a slow fall velocity the microplastic is rapidly exported from the model domain, while for a fast fall velocity the microplastic is removed more slowly and gets reworked within the active sedimentary layer. Due to the changing nature of the active layer, there are periodic shifts in the flux due to the erosion into new layers of sediment mixed with the active layer above ({numref}`sens-loc`). The regularity of this pattern is due to the simple 1D model set up, and it is unlikely that this would be observed in nature.

# Transport of microplastic in 2D - incorporation into Casear-Lisflood

The aim of the 1D modelling was to test the sensitivity and behaviour of the addition of microplastic transport to a model of sediment transport. The same code framework of the 1D transport model can be included within the landscape evolution model `CAESAR-Lisflood`, in this case the C++ version `HAIL-CAESAR` [@valters-2023]. The modified version of `HAIL-CAESAR` containing microplastic is currently a fork of the main `HAIL-CAESAR` repository [@armitage-2023]. Microplastic is included as a grain size that is transported within the water column as described in equation {eq}`trans-plastic`. However, the numerical scheme to include the transport is slightly different, as `CAESAR-Lisflood` solves for the water flux within the cells and not at the cell boundaries, using a left-to-right and up-to-down sweeping algorithm, rather than resolving the flux at the cell interfaces. This has an impact on the sensitivity to spatial resolution. 

```{figure} ./2dplots/initial-DEM.png
---
name: 2D-topo
width: 80%
align: center
---
Initial synthetic topography to test the inclusion of a source of microplastic. The gradeient along the V of the wedge is 1$^{\circ}$.
```

Building on the 1D model we first test the 2D model by including a source region of microplastic within a wedge-like topography. The initial condition is of a topography that is like a tilted and folded piece of paper, where the slope along the axis of the valley is 1$^{\circ}$ ({numref}`2D-topo`). The initial condition includes a slight surface roughness of a maximum amplitude of 0.1 m to help localise the flow of water. The active layer is set to be 0.1 m thick and the grain size distribution is as in {numref}`grains`. Precipitation rates are of 1 mm/hr or 0.1 mm/hr across the 5000 by 10000 m domain and fall velocities of 10<sup>-2</sup> and 10<sup>-4</sup> m/sec are tested. Microplastics are set to be only in the topmost active layer and over a 1000 by 2000 m rectangle within the centre of the model domain.

## Microplastic transport in low flows

```{figure} ./2dplots/plastic-r0.100_v1e-4.png
---
name: r0.1_v1e-4
width: 80%
align: center
---
Water flow and plastic concentration in the active layer after 60 hr when the prectipitation rate is 0.1 mm/hr and the fall velocity of the microplastic is 10<sup>-4</sup> m/sec. (a) Water height is displayed in the blue colormap and the topography is contoured at 25 m intervals. The black rectangle shows the bounding box for part b. (b) Volume concentration of microplastic that remains in the 0.1 m thick active layer.
```

The overland flow of water collects within the valley as would be expected. For low precipitation rates the flow height is low and the channel width is only one cell wide ({numref}`r0.1_v1e-4`). When the fall velocity is likewise low, there result is that the microplastic is stripped in the region where the water flow crosses the rectangular source zone. When the fall velocity is faster microplastic gets deposited within the channel ({numref}`r0.1_v1e-2`). 

```{figure} ./2dplots/plastic-r0.100_v1e-2.png
---
name: r0.1_v1e-2
width: 80%
align: center
---
Water flow and plastic concentration in the active layer after 60 hr when the prectipitation rate is 0.1 mm/hr and the fall velocity of the microplastic is 10<sup>-2</sup> m/sec. (a) Water height is displayed in the blue colormap and the topography is contoured at 25 m intervals. The black rectangle shows the bounding box for part b. (b) Volume concentration of microplastic that remains in the 0.1 m thick active layer.
```

```{figure} ./2dplots/plastic-flux-r0.100.png
---
name: flux_r0.1
width: 80%
align: center
---
Water, microplastic and silt flux from the model domain when the precipitation rate is 0.1 mm/hr. (a) Water flux, note there is only one curve as the fall velocity of microplastic does not significantly impact the topography to alter the water flux. (b) Microplastic flux, where no microplastic leaves the model domain on the 100 hr time window if the fall velocity is 10<sup>-2</sup> m/sec while there is a pulse of microplastic when the fall velocity is 10<sup>-4</sup> m/sec. (c) Silt flux, where the output from the two models are identical, the microplastic does not influence suspended sediment fluxes.
```

In the two model cases with a low precipitation rate of 0.1 mm/hr the water flow is very low, and there is limited erosion of the active layer. The erosion is so low such that the active layer is not eroded sufficiently to require the addition of new material from the strata below. The microplastic flux out from the model domain for a fall velocity of 0.01 m/sec is zero ({numref}`flux_r0.1`). When the fall velocity is 0.0001 m/sec the microplastic leaves the model domain in a time window of around 24 hrs ({numref}`flux_r0.1`).

## Microplastic transport in high flows

```{figure} ./2dplots/plastic-r1.000_v1e-4.png
---
name: r1.0_v1e-4
width: 80%
align: center
---
Water flow and plastic concentration in the active layer after 60 hr when the prectipitation rate is 1.0 mm/hr and the fall velocity of the microplastic is 10<sup>-4</sup> m/sec. (a) Water height is displayed in the blue colormap and the topography is contoured at 25 m intervals. The black rectangle shows the bounding box for part b. (b) Volume concentration of microplastic that remains in the 0.1 m thick active layer.
```

```{figure} ./2dplots/plastic-r1.000_v1e-2.png
---
name: r1.0_v1e-2
width: 80%
align: center
---
Water flow and plastic concentration in the active layer after 60 hr when the prectipitation rate is 1.0 mm/hr and the fall velocity of the microplastic is 10<sup>-2</sup> m/sec. (a) Water height is displayed in the blue colormap and the topography is contoured at 25 m intervals. The black rectangle shows the bounding box for part b. (b) Volume concentration of microplastic that remains in the 0.1 m thick active layer.
```

For the two models with a higher precipitation rate of 1 mm/yr the water depth is significantly higher, up to 0.3 m ({numref}`r1.0_v1e-4`). The higher flux of water means the channel is wider and more microplastic gets stripped from the active layer. For a high fall velocity a significant quantity of microplastic gets deposited within the channel ({numref}`r1.0_v1e-2`). 

```{figure} ./2dplots/active-layer-r1.000_v1e-2.png
---
name: act-lyr
width: 80%
align: center
---
Thickness of the active layer within the microplastic sorce region after 60 hours of model run time.
```

The increased water flux means that the active layer becomes significantly eroded, such that the strata below become incorporated within the active layer. Given that the incorporation of the lower strata is based on the criteria that the thickness of the active layer falls below one quarter of its initial thickness then the layer below is added. This means that cells will change in thickness of active layer and grain size distribution. Therefore, if there is a slight difference in thickness in adjacent cells causing once cell to meet the criteria while the neighbouring cell does not, there erosion rates will vary between the two cells due to the different grain size proportions. This will then cause a cascade in different local erosion rates, creating the *rough* nature of the active layer thickness of the channel bed at the end of the model run ({numref}`act-lyr`).

```{figure} ./2dplots/plastic-flux-r1.000.png
---
name: flux_r1.0
width: 80%
align: center
---
Water, microplastic and silt flux from the model domain when the precipitation rate is 0.1 mm/hr. (a) Water flux, where the water flux is not identical for the two model runs due ot the topographic change from erosion of microplastics from the bed at the hgher water flow rates. (b) Microplastic flux, where both model transport the microplastic sufficiently that it starts to be exported from the domain. When the fall velocity is slow, 10<sup>-4</sup> m/sec, there is a pulse of microplastic export. (c) Silt flux, where the output of silt is modified by the presence of the microplastic.
```

This local change in active layer thickness and local grain size distribution has the effect of creating a sediment flux output that is unsteady through time ({numref}`flux_r1.0`). The movement in the sediment flux is not due to the water routing, but due to the shifts in grain size distribution as the active layer is adjusted locally due to erosion. Furthermore, the model would suggest that the addition of the small amount of microplastic within the source region will impact the release of silt from the model domain ({numref}`flux_r1.0`). This is due to the deposition of microplastic impacting the active layer thickness and so the outcome for the erosion of silt and the other sediment grains is not the same for the two model scenarios.

# Landscape scale application - Têt catchment, France

## Topography and hydrology

```{figure} ./2dplots/tet-vinca-catchment.png
---
name: dem
width: 80%
align: centre
---
Procesessed digital elevation model of the catchment of the Têt river, where the region upstream of the Vinça dam has been removed.
```

The Têt River is a typical coastal Mediterranean river with a drainage catchment area lower than 5000 km<sup>2</sup>. The coastal plain of the Têt watershed is characterized by agricultural activities, as well as the location of the largest city of the district (Perpignan, about 150,000 inhabitants). Two major dams exist in the Têt River basin and they lie upstream the densely populated coastal plains. The most downstream dam is at Vinça. This dam is used create a lake environment for summer activities. To simplify the application of the microplastic transport we will focus on the catchment below the dam, see {numref}`dem`, as this region has low slopes and therefore is more appropriate for the diffusive-wave approach of the overland flow model. Furthermore the alluvial plain contains the source region of microplastics due to the increased quantity of roads and population centres here.

```{figure} ./2dplots/tet-rainfall-zones.png
---
name: spatial-rain
width: 80%
align: centre
---
Zones used for each rainfall guage within the model catchment, where the rainfall guages are numbered from 66002001 to 6623001 from the identification numbers of the weather stations from Météo France (see [Publithèque of Météo France](https://publitheque.meteo.fr/okapi/accueil/okapiWebPubli/index.jsp)). The zonation is a simple nearest neighbour algorithm using the python library `scipy`. For the daily rainfall data time series for each guage see {numref}`rainfall`.
```

```{figure} ./2dplots/tet-vinca-rainfall.png
---
name: rainfall
width: 100%
align: centre
---
Daily rainfall data time series data used to force the model for the nine weather stations numbered from 66002001 to 6623001 from the identification numbers of the weather stations from Météo France (see [Publithèque of Météo France](https://publitheque.meteo.fr/okapi/accueil/okapiWebPubli/index.jsp)). For regions for which each timeseries is applied see {numref}`spatial-rain`.
```

The model is forced with a daily time series of rainfall from eight weather stations that are within or near the catchment ({numref}`spatial-rain` and {numref}`rainfall`), where the data is from the [Publithèque of Météo France](https://publitheque.meteo.fr/okapi/accueil/okapiWebPubli/index.jsp). The zones for which each time series is applied is calculated using the nearest neighbour algorithm of the `scipy` python library. Given the low resolution of the rainfall data we chose to keep the spatial resolution at 200 m cell resolution. Noting that `CAESAR-Lisflood` is resolution dependent [@skinner-2023], we think that a resolution of 200 m is appropriate for testing the applicability of the simple microplastic transport model given the broad assumptions on transport and the lack of detailed rainfall data at a high spatial and temporal resolution.

To avoid artificial damming of the river due to artefacts within the digital elevation model, we pre-process the DEM. The DEM is downloaded from the [BD Alti](https://geoservices.ign.fr/bdalti) data portal of the IGN (Institute Nationale d'Infromation Geégraphique et Forestiére). We down-sample the DEM to 200 m cells using both the mean and minimum values. Using the mean and minimum values we run first the pit-filling algorithm `SinkFiller` [@barnes-etal-2014] from the `LandLab` library [@hobley-etal-2017; @barnhart-etal-2020; @hutton-etal-2020] on both resampled DEMs, using the D4 routing to be consistent with the D4 nature of the Lisflood-FP algorithm implemented in Casear-Lisflood. Focusing on the minimum DEM we run the Lisflood-FP algorithm looped over two years of rainfall to obtain the river network. Regions where the water depth is greater than 0.05 m are then used to replace the mean value with the minimum value. Therefore, the pre-processed DEM consists of the mean value from the high resolution DEM on the hill slopes, and the minimum value within the channels.

```{figure} ./2dplots/tet-observations.png
---
name: obs
width: 100%
align: centre
---
Averaged catchment wide rainfall and observations of water flux, suspended sediment, and microplastic concentration 10 km inland from the river outlet [42.7136938N, 2.9827452E, @constant-etal-2020]. (a) Catchment averaged precipiation rate. (b) Water flux from the site *hydrométrique* Y047 4030 : La Têt à Perpignan (Pont-Joffre) ([https://www.hydro.eaufrance.fr]) and (c) sediment flux from the same site, blue circles, with the interpolated values that correspond to the microplastic flux in part d, red circles. (d) Microplastic flux from @constant-etal-2020.
```

Punctual measurement of microplastic concentrations at 10 km inland from the Têt river outlet were carried out in 2016 [@constant-etal-2020]. Combined with the gauging station there are therefore observations for water flux, suspended sediment load, and microplastic ({numref}`obs`). We first calibrate the hydrological model against the observations of water flux. The two key parameters to which the model is sensitive are the assumed quantity of rainfall that passes through the evapotranspiration into run-off, and the storage of water within the subsurface via the `TOPMODEL` parameter $m$. For evapotranspiration we make the simplified assumption that it is constant in time and reduce the precipitation by using 80, 60 or 40% of observed daily rate. `TOPMODEL` is a simple set of logarithmic functions that approximate the hydrographic response to precipitation to give the characteristic recession curve within a river after precipitation input [e.g. @beven-etal-1984]. The parameter $m$ controls the hydrographic response, where smaller $m$ leads to a faster return in the run-off to the steady state after a rain-fall input. We test the range of $m$=0.006 to 0.014.

```{figure} ./2dplots/tet-fit.png
---
name: nse-fit
width: 100%
align: centre
---
Nash-Sutcliff model efficiency (NSE) for the model for different assumptions on rainfall loss due to evapotranspiration, 40, 60 or 80% of rainfall is transmitted from the surface into the subsruface as input to the `TOPMODEL` component of `CAESAR-Lisflood` and surface storage via the `TOPMODEL` parameter $m$ that controls the peak and duration of the recession curve. If the NSE is negative the model fit is no better than the a constant mean value, the closer to 1, the better the fit.
```

By comparing the rainfall time series averaged over the catchment below the Vinça dam and the water flux observed at Perpignan, we can see that out flows of the dam during the summer months impact the observed water flux, exemplified by the large flow event in mid May, 2016 ({numref}`obs`). To compare the model water flux with the observations, we therefore focus on the spring and autumn months, from 15/2/2016 to 1/5/2016 and from 1/9/2016 to 1/11/2016. The quality of model fit is estimated using the Nash-Sutcliff model efficiency (NSE), where if the fit is negative, then the model is no better than the time averaged mean, while good fits are closer to 1. We find that most models do not give a good fit, however if we assume that 40% of precipitation effectively reaches the surface, and a `TOPMODEL` $m$ parameter of 0.008 to 0.010 then the NSE value is 0.46 to 0.47.

## Microplastic source and concentrations

Microplastics primarily enter river systems through wastewater and the miss-management of waste. The quantity of microplastics entering the rivers can be significantly reduced through wastewater treatment, however microplastics related for fabrics are still transported into the rivers [@woodward-etal-2021]. From looking at samples from riverbank deposits, it was found that in the River Tame, UK, microplastic is sourced from both treated wastewater and more importantly untreated waste. Two sources are highlighted [@woodward-etal-2021]: (1) continuous transport at low concentrations of synthetic fibres from treated wastewater effluent; and (2) episodic flood-driven transport of the full microplastic assemblage entrained from contaminated channel beds. The evidence from this catchment would suggest that there are point sources of continuous microplastic from urban treatment works and factories. Flood waters will however source microplastics that have become deposited within the river catchment and hence there are events where microplastic is sourced catchment wide. In a similar study on the Brisbane River, Australia, it was found that the concentration of microplastic deposited at the riverbank did not vary spatially along with land use, however the types of plastic found vary [@he-etal-2020]. In rural areas microplastic deposition was dominated by polyethylene [@he-etal-2020].

In water treatment stations, the dense microplastics are typically removed through settling and remain in the sludge. Microplastics within fabrics are significantly harder to remove, hence they seep into the rivers along with the treated water. The sludge is however processed and used in many countries as a fertiliser. This raises the potential that microplastic enters rivers from runoff withing agriculture land [@nizzetto-etal-2016], and might explain the lack of variability in microplastic concentrations in the Brisbane River. Atmospheric falls could also act as a source of microplastic in soil and along catchment slopes. Regardless, microplastics are ubiquitous within the environment, and with the current lack of continuous monitoring within any fluvial system there is current estimates on the sources of microplastic are extrapolated from a handful of measurements.

```{figure} ./2dplots/pop-density.png
---
name: pop
width: 80%
align: centre
---
Population density of the Tet catchment below the Vinca dam at a 200 m grid resolution. The data is from the [INSEE Filosofi population database](https://www.insee.fr/fr/statistiques/6215168?sommaire=6215217).
```

To estimate the source of microplastics within the catchment we make the simple assumption that microplastics in the active layer of the model are related to the population density [@weiss-etal-2021]. Using the INSEE [Filosofi](https://www.insee.fr/fr/statistiques/6215168?sommaire=6215217) population database averaged of a 200 m raster [@insee-2017], we created a map of the population density ({numref}`pop`). We then relate the population density to the volume concentration of microplastic within the active layer, where if the the population density is greater than 2 persons per square meter then the volume concentration of microplastics are high, while if the population density is between 0.5 and 2 persons per square meter the conentration is low, and below this population density we assume zero microplastic pollution (see {numref}`mp-contam`). There is very little information on the quantity of microplastic in surface soils within rural France [e.g. @kedzierski-etal-2023]. Mass concentrations of microplastics observed in soil samples globally, compiled in [@rohais-etal-2024]. From a total of 107 measurements the mean soil mass concentration of microplastic is 3.77 g/kg. Mass concentration estimates derived from abundance-to-mass relationships for 442 measurements [@kedzierski-etal-2023] range between 456 and 1938 mg/m<sup>3</sup> for sites subject to contamination using wastewater for irrigation, with a median value of 1066 mg/m<sup>3</sup>. For sites subject to contamination via plastic mulching activities, values range from 2432 to 10,347mg/m<sup>3</sup>, with a median value of 5692 mg/m<sup>3</sup> [@kedzierski-etal-2023]. However these samples might be biased towards greater pollution, as measurements from dredge samples of the Aa River in northern France, that is in a industrial area, contained up to 0.1 g/kg of microplastic [@constant-etal-2021]. Based on these numbers, we make some broad estimates of the volume concentration of the microplastic and create maps with what we will term *high*, *mid* and *low* contamination (see {numref}`mp-contam`).

```{list-table} Range of tested microplastic concentrations in active layer
:header-rows: 1
:name: mp-contam

* - Population density range (persons/m$^{2}$)
  - *high* MP vol. concetration (ppm)
  - *mid* MP vol. concetration (ppm)
  - *low* MP vol. concetration (ppm)
* - $> 2$
  - $100$
  - $10$
  - $1$
* - $> 0.5$
  - $10$
  - $1$
  - $0.1$

```

The model is run assuming the active layer has a thickness of 0.5 m and it is only the initial active layer that contains microplastics within the regions defined by their population density. The microplastic then gets transported by the overland flow and settles assuming a fall velocity of 10<sup>-4</sup> and 10<sup>-6</sup> m/sec. We loop the model over duration of the rainfall series from October 2015 to April 2018 ({numref}`rainfall`) to have model wind up prior to a model with a more connected fluvial network. The punctual observations of microplastic concentrations can be used to attempt to verify if the model is capable of generating sensible values for the quantity of microplastic in transport. We explore both the range of concentrations of microplastic in the active layer related to the population density and two fall velocities. Given the observations are punctual and do not capture peak flow conditions we will not look at the time series, but instead look at the relationships modelled and observed between water flux and microplastic concentrations ({numref}`concfit` and {numref}`vdropfit`).

```{figure} ./2dplots/tet-MP-concfit.png  
---
name: concfit
width: 80%
align: centre
---
Comparison of observed and modelled microplastic concentrations at the outlet of the Tet catchment [@constant-etal-2020] for different intial concentrations of microplastics ({numref}`mp-contam`) assuming a fall velocity of $10^{-4}$ m/sec. The volume concentration is converted to a mass per volume of water assuming the micropalstic density is 1300 kg/m<sup>3</sup>.
```

From a comparison of the model microplastic fluxes and the observations it is clear that the *high* initial contamination of microplastics generates more microplastic in suspension than the observations ({numref}`concfit`, light blue). The *low* contamination model generates too little microplastic ({numref}`concfit`, salmon red), while the *mid* contamination generates a trend in microplastic concentrations that is coincident with the observations ({numref}`concfit`, light green). This implies that under the assumptions of this model, at a resolution of 200 m cells the microplastic volume concentrations related to populated areas could be in the range of 1 to 10 ppm. The fall velocity of the microplastics will however impact the flux out of the system. If the fall velocity is of the order of 10<sup>-6</sup> m/sec the microplastic concentration at the outlet increases by an order of magnitude ({numref}`vdropfit`). This change is however within the range of observed concetrations. This could imply that due to alteration, flocculation, and weathering the fall velocity of the microplastics could be in the range of 10<sup>-4</sup> to 10<sup>-6</sup> m/sec.

```{figure} ./2dplots/tet-MP-vdropfit.png
---
name: vdropfit
width: 80%
align: centre
---
Comparison of observed and modelled microplastic concentrations at the outlet of the Tet catchment [@constant-etal-2020] for different fall velocities assuming an intial concentrations of microplastics that is in the *mid* range, where for a population density greater than 2 person/m<sup>2</sup> the volume concentration is 10<sup>-5</sup> ({numref}`mp-contam`). The volume concentration is converted to a mass per volume of water assuming the micropalstic density is 1300 kg/m<sup>3</sup>.
```

If we assume that the *mid* model of initial microplastic contamination and a fall velocity of 10<sup>-4</sup> m/sec is representative of the Têt catchment discounting the area above the Vinça dam, then we find that microplastic becomes entrained and deposited within the Têt fluvial system ({numref}`tet-mp`). As with the simple test on the simple rectangular catchment, we can see that the model creates a channel with a distribution of regions of low and high active layer thickness as this layer becomes eroded and is unified with the strata below ({numref}`tet-mp`a). The water height within the alluvial plain is of the order of meters, which is consistent with the Têt river, given the low resolution of the model ({numref}`tet-mp`b). Within this channel, after a model evolution of 5 years the microplastic has started to accumulate within reaches of the Têt, particularly to the west of the more populated region of Perpignan and at the outlet ({numref}`tet-mp`c).

```{figure} ./2dplots/tet-mid-200-r40-v1e-4-t1815.png
---
name: tet-mp
width: 100%
align: centre
---
Change in the active layer, water depth, and concentration of microplastics for the *best fit* model scenario. (a) Thickness of the active layer after the 5 year model run ti;e, where the initial thickness was 0.5 m. (b) Water height within the model domain. (c) Volume concentration within the active layer as a function of the initial condition and the transport and deposition of the microplastic after 5 years of daily rainfall history. The microplastic is observed to become deposited along the river network below each small populated zone.
```

# Discussion

In this paper we have outlined a relatively simple method for including microplastic transport within a landscape evolution model, where the overland flow of water is solved and a shear stress on the bed can be approximated. The motivation for developing this model was to explore the transport and storage of microplastic within fluvial systems. Studies that have looked at the storage of microplastic within rivers have found locally very large accumulations [@hurley-etal-2018], and it has also been noted that microplastic accumulation can be associated with the accumulation of silt [@he-etal-2020]. We therefore decided to explore if we could apply simple rules developed for sediment transport to capture the transport of microplastic. The model developed here clearly has its limitations, which we will outline later. First we will explore what the model implies for microplastic contamination.

The fall velocity of the microplastic is the the key parameter that controls the storage of micropalstic within the fluvial system. The simple 1D model demonstrated that if the fall velocity is sufficiently slow, 10<sup>-4</sup> m/sec then over a distance of 5 km all of the microplastic will be transported out of the domain within a day ({numref}`sens-loc`). For a 2D flow there was minimal retention of microplastic when the fall velocity was 10<sup>-4</sup> m/sec for both low and high water flux ({numref}`flux_r0.1` and {numref}`flux_r1.0`). This can be seen in the small flux of microplastic that continues to be released after the main pulse of microplastic. When the fall velocity is two orders of magnitude higher then there is significant retention of microplastic. When the model is scaled to the Têt catchment we find that a fall velocity of between 10<sup>-4</sup> and 10<sup>-6</sup> m/sec best matches the observed concentration of microplastics within the river ({numref}`vdropfit`). In settling columns, the fall velocity of microplastic particles are of the order of 10<sup>-3</sup> to 10<sup>-1</sup> m/sec [@waldschlager-2019-settling; @constant-etal-2023], which is similar to fine sand and soil [@constant-etal-2023]. Settling columns ignore the turbulent nature of the overland flow that will keep more of the microplastic in suspension and may explain why at the landscape scale the model implies a much lower fall velocity. Otherwise, the model developed here would suggest that a really significant quantity of microplastic will be stored within the fluvial network.

The model also implies that there is of the order of 1 to 10 ppm volume concentration of microplastic within the top 50 cm of the soil at a 200 m<sup>2</sup> resolution. A correlation between population density and the generation of microplastic within miss-managed waist has been recognised, however the estimates are reported as a mass concentration [e.g. @weiss-etal-2021]. The difficulty is in converting this to a volume concentration as a model input, due to the large range of possible soil densities. In this study, by finding the model source concentrations that fit the observed suspended microplastic load, we can estimate the distribution of micropalstic within the landscape. Making the simple assumption that populated areas lead to mictoplastic pollution, we find that reaches of the river Têt will become contaminated with microplastics down-system of these source zones. For example, down stream of Ille-sur-Têt and Millas, the model suggest that microplastic will be stored within the river sediments ({numref}`tet-mp`). From a compilation of river sediment observations [@eo-etal-2019; @kabir-etal-2022; @he-etal-2020; @klein-etal-2015; @niu-etal-2021; @rao-etal-2019; @rodrigues-etal-2018; @sarkar-etal-2019], the observations would suggest that there is on average the order of 50 mg/kg of microplastic within the sediments [see @rohais-etal-2024], for the full compilation). If we assume that the density density difference between the microplastic and sediment is roughly a half and ignore the porosity then the volume concentration is of the order of 25 ppm. This is in the range of the model values and redistribution of micropalstic within the model ({numref}`tet-mp`). It would be ideal if in future work the sediment could be sampled in these regions to obtain an idea of the concentration of microplastic.

Microplastic is included within the model only as part of the surface active layer. As the active layer is eroded below the threshold thickness there is a instantaneous reduction in the volume concentration of microplastic within the updated active layer accompanied by a change in the transport properties of the active layer within the cell. For the simple 1D model the erosion of the active layer creates a cascade of knick-points ({numref}`active-layer`). For the 2D model, the initial condition has an addition roughness, that causes the flow to not create a smooth increase erosion rate down system, but instead generate a distribution of cells that become eroded and modified ({numref}`act-lyr`). The impact of active layer on fluvial erosion is not the focus of this paper, however for the simple 2D case, there is evidence of a similar pattern of waves of change in thickness of the active layer migrating up the catchment ({numref}`act-lyr`). However, when the model is applied to the Têt catchment, the resolution becomes too low to make any meaningful interpretations on the patterns of erosion within the river valley. The overall increase in microplastic concentration downstream in the river, nevertheless, suggests that microplastic pollution is accumulating as water flows downstream and could deserve special attention for monitoring and remediation.

The model does however have a few limitations that are worth discussing. The first is related to the diffusive-wave approximation to solving the shallow water equations for overland flow. In order to have stable solutions it is necessary to limit the water flux such that the Froude number for the flow is less than 1. This means that when the slope exceeds 1$^{\circ}$ the water velocity does not increase with increasing slope. As such the transport model will underestimate the quantity of sediment and microplastic eroded within steep topography. Second, simulations with `CAESAR-Lisflood` are known to be resolution dependent [@skinner-2023], therefore while the 1D tests of the numerical implementation of the microplastic transport suggest that above a certain resolution the model is stable, this might not be the case for the 2D runs. This touches a related problem of keeping the model resolution coherent with the resolution of the observations. For the test against the Têt catchment we have kept the resolution low, as we have daily rainfall data at point locations, and limited observations of microplastic flux. Therefore it is difficult to argue for a very high resolution for the model, when the model inputs are not of the same quality.

# Conclusions

In sedimentology rivers are the pathway for sediment from source-to-sink and as such the transport of sediment by flowing water has been given a great deal of attention. Landscape evolution models have since been developed with the aim of capturing the basic principles of the transport of sediment to capture the evolution of fluvial systems. Microplastic form dense particulates within the water column that may remain in suspension for a considerable amount of time, equivalent to suspended sediments, or may settle and become trapped within fluvial deposits. From this descriptive similarity we have developed a simple set of laws to capture the transport of microplastic, the erosion of microplastic from the river bed, and the deposition of microplastic. This allows us to insert microplastic into a landscape evolution model such as `CAESAR-Lisflood` and then track the deposition of the microplastic within the fluvial network. By applying this model to the Têt catchment we find that there is potentially high quantities of microplastic within the fluvial deposits upstream of Perpignan. By calibrating the model against observed microplastic fluxes, we can estimate the volume concentration of microplastic within the source regions, assuming microplastic is correlated with population density [e.g. @weiss-etal-2021]. Future work will focus on calibrating the model simulations with increased observations of microplastic quantites in fluvial sediments and monitoring of fluvial fluxes.



+++{"part":"declarations"}
We would like to acknowledge Wolfgang Ludwig and Mel Constant for sharing their data and discussion on microplastic, and Declan Valters and Tom Coulthard for sharing their code and time in explaining various input variables and parameters.

The authors declare that no funds, grants, or other support were received during the preparation of this manuscript. The authors have no relevant financial or non-financial interests to disclose. 

Both authors contributed to the study conception and design. Methods and model development were performed by John Armitage. Analysis and interpretation was performed by John Armitage and Sébastien Rohais. The first draft of the manuscript was written by John Armitage and both authors commented on previous versions of the manuscript. Both authors read and approved the final manuscript

*The 1D Microplastic transport model will be made available before publication* 
The code for `CAESAR-Lisflood` (`HAIL-CAESAR`) with the modifications is available here: [https://github.com/johnjarmitage/HAIL-CAESAR/tree/plastic].
Notebooks to plot the output of the model runs are available here: [https://github.com/johnjarmitage/caesarPy/tree/master/notebooks/tet], and the workflow for the DEM pre-processing is available here: [https://github.com/johnjarmitage/caesarPy/tree/master/dem-preprocessing]

+++