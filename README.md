# The Transfer Matrix Method
This project is associated with calculation of the real and imagenry part of the refractive index of CaZn2P2 PAPER HERE, howoever, the concepts and the calculation applies to any system.

The modeling the propoagtion of light through a material can become very complicated onece we have more than one interface. We can solve the propagation equations analytically by had for 2 layers (or repetations of 2 layers) as described here: (https://ocw.mit.edu/courses/3-024-electronic-optical-and-magnetic-properties-of-materials-spring-2013/bc1be0fe02cf4f8845cc7c8c5ed97008_MIT3_024S13_2012lec23.pdf) but once we have more than 2 we can't do it by hand and at a certin potin we cant determine the analytical solution with compouters at a reasonable time frame and need to resort to a numerical solution. 


The light passes through air to the film and through SiO2 substrate and through the air again for transmission measurements. We can use the data for reflection, but the numerical solution still holds even if we just look at transmission. So we have to concern ourselves with 3 interfaces and 2 layers through which the light propagates. 

Interface 1: air-CZP
Layer 1: CZP
Interface 2 CZP-SiO2
Layer 2: SIO2 (I assumed to be 2 mm thick)
Interface 3: SiO2-air

Each of these interfaces and layer propagations give us a matrix D that defines how the incoming light is related to the outgoing light. Fortunately its not too complicated to derive these matrices (https://ocw.mit.edu/courses/3-024-electronic-optical-and-magnetic-properties-of-materials-spring-2013/bc1be0fe02cf4f8845cc7c8c5ed97008_MIT3_024S13_2012lec23.pdf). The interfaces can be simplified to the matrix 
I = (1/t) * [ [1, r], [r, 1]] 
where r and t are obtained from the fresnel equations
r = (n1 - n2 / (n1  + n2) and  t = 2 * n1 / (n1 *  + n2) 
(I am assuming a normal incidence angle here for the simplicity of writing the equation but in the code we can plug in whatever angle we want). Note for n_CZP we use the complex form where n_CZP = n + ik (here n and k are what we are solving for) and for n_SiO2, we can simply looked up a database and used its refractive indices for the range of wavelengths we have.
The propagation matrices are defined by 
P = [ [exp(i*beta*d), 0],  [0, exp(-i*beta*d)]  
where beta = 2 * pi * n_material / wavelength and d is the thickness of the material. After making 4 matrices for the interfaces and layer propagations mentioned above, we can simply perform matrix multiplication to find the overall transfer matrix, M. From M we can find 
R = | M_1_0 / M_0_0 | ^2 and 
T = (n_SiO2 / n_air ) * | 1 / M_0_0| ^ 2. 
So we can just use the experimental T to calculate the loss as it contains M which in turn contains n and k (which we are fitting). And lastly the calculation is highly dependent on the thickness of the CZP layer and we need to know the actual thickness for an accurate calculation.

(I tried using n and k values of the preceding wavelengths as initial guesses, but this seems to lead to bad error propagation as the error from each calculation compounds). 

A common approach for determining the real and imagenry part of the refractive index calculates interface and a propagation together for each layer (https://www.mathworks.com/matlabcentral/fileexchange/50923-jreftran-a-layered-thin-film-transmission-and-reflection-coefficient-calculator), for that reason it tells us to only consider the film as the layer and exclude the substrate and air (only including their refractive indices in coefficients eta_1 and eta_N). This also  considers the substrate as a semi infinite non absorbing material. This becomes problematic when (1) we have a substrate that becomes slightly absorbing at certain wavelengths (like SiO2 at high energy regimes) and (2) it excludes the effect of the final substrate/air interface. This is a good apporximation, nonetheless, a more complete approach would be to treat the interfaces and propagations separately,  including the substrate propagation. I do this with in the code here. 

Below is comparison of the calculated transmission/reflection (using the calculated n and k) and the measured transmission/reflection

![image001](https://github.com/user-attachments/assets/74736398-a193-4cc9-9583-1f9e2b88248e)
![image005](https://github.com/user-attachments/assets/40f874a0-e81d-4a5a-9e1e-8f39bf72ee08)
