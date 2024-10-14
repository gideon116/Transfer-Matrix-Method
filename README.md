# The Transfer Matrix Method
This project is associated with the calculation of the real and imaginary parts of the refractive index of CaZn₂P₂. The concepts and the calculations apply to any system.

The modeling of the propagation of light through a material can become very complicated once we have more than one interface. We can solve the propagation equations analytically by hand for two layers (or repetitions of two layers) as described here: (https://ocw.mit.edu/courses/3-024-electronic-optical-and-magnetic-properties-of-materials-spring-2013/bc1be0fe02cf4f8845cc7c8c5ed97008_MIT3_024S13_2012lec23.pdf). However, once we have more than two layers, we can't do it by hand, and at a certain point, we can't determine the analytical solution with computers in a reasonable time frame and need to resort to a numerical solution.

The light passes through air to the film, through the SiO₂ substrate, and through the air again for transmission measurements. We can use the data for reflection, but the numerical solution still holds even if we just look at transmission. So, we have to concern ourselves with three interfaces and two layers through which the light propagates.

- **Interface 1**: Air-CZP
- **Layer 1**: CZP
- **Interface 2**: CZP-SiO₂
- **Layer 2**: SiO₂ (assumed to be 2 mm thick)
- **Interface 3**: SiO₂-air

Each of these interfaces and layer propagations gives us a matrix D that defines how the incoming light is related to the outgoing light. Fortunately, it's not too complicated to derive these matrices (https://ocw.mit.edu/courses/3-024-electronic-optical-and-magnetic-properties-of-materials-spring-2013/bc1be0fe02cf4f8845cc7c8c5ed97008_MIT3_024S13_2012lec23.pdf). The interfaces can be simplified to the matrix:

\[
I = \frac{1}{t} \begin{bmatrix} 1 & r \\ r & 1 \end{bmatrix}
\]

where \( r \) and \( t \) are obtained from the Fresnel equations:

\[
r = \frac{n_1 - n_2}{n_1 + n_2}
\]
\[
t = \frac{2 n_1}{n_1 + n_2}
\]

(I am assuming a normal incidence angle here for simplicity, but in the code, we can plug in whatever angle we want). Note: For \( n_{\text{CZP}} \), we use the complex form where \( n_{\text{CZP}} = n + ik \) (here \( n \) and \( k \) are what we are solving for), and for \( n_{\text{SiO₂}} \), we can simply look up a database and use its refractive indices for the range of wavelengths we have.

The propagation matrices are defined by:

\[
P = \begin{bmatrix} \exp(i \beta d) & 0 \\ 0 & \exp(-i \beta d) \end{bmatrix}
\]

where:

\[
\beta = \frac{2 \pi n_{\text{material}}}{\text{wavelength}}
\]

and \( d \) is the thickness of the material. After creating four matrices for the interfaces and layer propagations mentioned above, we can simply perform matrix multiplication to find the overall transfer matrix, \( M \). From \( M \), we can find:

\[
R = \left| \frac{M_{10}}{M_{00}} \right|^2
\]
\[
T = \left( \frac{n_{\text{SiO₂}}}{n_{\text{air}}} \right) \left| \frac{1}{M_{00}} \right|^2
\]

We can use the experimental \( T \) to calculate the loss as it contains \( M \), which in turn contains \( n \) and \( k \) (which we are fitting). Lastly, the calculation is highly dependent on the thickness of the CZP layer, and we need to know the actual thickness for an accurate calculation.

(I tried using \( n \) and \( k \) values of the preceding wavelengths as initial guesses, but this seems to lead to bad error propagation as the error from each calculation compounds.)

A common approach for determining the real and imaginary parts of the refractive index calculates the interface and propagation together for each layer (https://www.mathworks.com/matlabcentral/fileexchange/50923-jreftran-a-layered-thin-film-transmission-and-reflection-coefficient-calculator). For that reason, it tells us to only consider the film as the layer and exclude the substrate and air (only including their refractive indices in coefficients \( \eta_1 \) and \( \eta_N \)). This also considers the substrate as a semi-infinite, non-absorbing material. This becomes problematic when (1) we have a substrate that becomes slightly absorbing at certain wavelengths (like SiO₂ at high energy regimes) and (2) it excludes the effect of the final substrate/air interface. This is a good approximation; nonetheless, a more complete approach would be to treat the interfaces and propagations separately, including the substrate propagation. I do this within the code here.

Below is a comparison of the calculated transmission/reflection (using the calculated \( n \) and \( k \)) and the measured transmission/reflection:

![image001](https://github.com/user-attachments/assets/74736398-a193-4cc9-9583-1f9e2b88248e)  
![image005](https://github.com/user-attachments/assets/40f874a0-e81d-4a5a-9e1e-8f39bf72ee08)
