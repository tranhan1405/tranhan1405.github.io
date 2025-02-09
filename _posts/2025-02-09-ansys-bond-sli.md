---
title: "ANSYS Workbench: Bond-Slip Behavior Between Rebar and Concrete (SOLID65) - Including Concrete Cracking"
date: 2025-02-09
permalink: /posts/2025/02/ansys-bond-slip/
tags:
  - ANSYS
  - Concrete
  - SOLID65
  - Bond slip behavior
---

The **SOLID65** element in **ANSYS Workbench** is designed to model concrete behavior, particularly to simulate cracking due to tensile stresses. This process involves defining material properties for both the concrete and the embedded rebar while ensuring appropriate contact and interaction between them.

To capture the **bond-slip behavior** between the rebar and concrete, **COMBINE39** elements are employed. These elements represent a non-linear spring behavior that accurately simulates how bond strength evolves and slip occurs as the applied load increases.

I demonstrate the finite element (FE) results in the YouTube video.
For further details, refer to this research paper and download the tutorial via the provided link:

Paper: [Experimental and numerical study of bond between masonry and near-surface mounted steel bars](https://doi.org/10.1016/j.cscm.2020.e00468)

Tutorial download:

## YouTube Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZBKRe3CBitc?si=YmkkEf5XB2Ak4naL" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

APDL code
Define SOLID65 APLD code:

<div style="border: 1px solid #ddd; padding: 10px; background-color: #f9f9f9;">
  <pre><code>
/prep7

ET,SOLID65_MATID,SOLID65
R,SOLID65_MATID,0,0,0,0,0,0
RMORE,0,0,0,0,0
MP,EX,SOLID65_MATID,3504
MP,PRXY,SOLID65_MATID,0.2
TB,CONCR,SOLID65_MATID,1,9
TBTEMP,22
TBDATA,1,0.2,0.8,0.385,-1    ! -1: removes the crushing capability
TB,MISO,SOLID65_MATID,1,7,0
TBTEMP,22

TBPT,,0.00036,1.26126
TBPT,,0.00060,1.96350
TBPT,,0.00130,3.37838
TBPT,,0.00190,3.84038
TBPT,,0.00200,3.85000
TBPT,,0.00243,3.85000
TBPT,,1.00000,3.85000



/SOLU
OUTRES,all,all
 </code></pre>
  </div>

Plot crack:
<div style="border: 1px solid #ddd; padding: 10px; background-color: #f9f9f9;">
  <pre><code>
!Change from black background to white background
/RGB,INDEX,100,100,100, 0   
/RGB,INDEX, 80, 80, 80,13   
/RGB,INDEX, 60, 60, 60,14   
/RGB,INDEX, 0, 0, 0,15  
/replot 
/POST1
/SHOW,png
/VIEW,1,1,0
/ANGLE, 1, 90, YS,
/gfile,600
!set,0.5
!SET,1,,,,,,100 !Outputs resuls at n^th steps (for example: at 100th step)
SET,,,,,0.1 ! Outputs resuls at time (second)
/DEVICE,VECTOR,on
PLCRACK,0,0 !PLCRACK, LOC, NUM
            !LOC: 0 — Plot symbols at integration points (default).
                 !1 — Plot symbol at element centroids (averaged).
            !NUM: 0— Plot all cracks (default) 
                 !1 - Plot only the first crack.
                 !2 - Plot only the second crack
                 !3 - Plot only the third crack.
 </code></pre>
  </div>



