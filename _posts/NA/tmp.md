$$x_{n+1}=x_n-\frac{f(x_n)}{f'(x_n)}$$



$$\begin{aligned}f_1(x_1,x_2)&=0\\f_2(x_1,x_2)&=0\end{aligned}$$



$$\begin{aligned}f_1(x_1,x_2)=&f_1(x_{10},x_{20})+(x_1-x_{10})\frac{\partial f_1}{\partial x_1}(x_{10},x_{20})+(x_2-x_{20})\frac{\partial f_1}{\partial x_2}(x_{10},x_{20})\\ +&\frac{1}{2!}(x_1-x_{10})^2\frac{\partial^2 f_1}{\partial x_1^2}(\xi_{x_1},\xi_{x_2})+\frac{1}{2!}(x_1-x_{10})(x_2-x_{20})\frac{\partial^2 f_1}{\partial x_1\partial x_2}(\xi_{x_1},\xi_{x_2})\\+&\frac{1}{2!}(x_1-x_{10})(x_2-x_{20})\frac{\partial^2 f_1}{\partial x_2\partial x_1}(\xi_{x_1},\xi_{x_2})+\frac{1}{2!}(x_2-x_{20})^2\frac{\partial^2 f_1}{\partial x_2^2}(\xi_{x_1},\xi_{x_2})\end{aligned}$$



$\xi_{x_2}$

$$\begin{aligned}=\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]\left[\begin{matrix}x_1-x_{10}\\x_2-x_{20}\end{matrix}\right]\end{aligned}$$



$$=\left[\begin{matrix}x_1-x_{10} & x_2-x_{20}\end{matrix}\right]\left[\begin{matrix}\frac{\partial f_1^2}{\partial x_1^2} & \frac{\partial f_1^2}{\partial x_1x_2}\\ \frac{\partial f_1^2}{\partial x_1x_2}& \frac{\partial f_1^2}{\partial x_2^2}\end{matrix}\right] \left[\begin{matrix}x_1-x_{10} \\ x_2-x_{20}\end{matrix}\right]$$



$$\begin{aligned}f_2(x_1,x_2)=&f_2(x_{10},y_{10})+(x-x_{10})\frac{\partial f_2}{\partial x_1}(x_{10},x_{20})+(y-y_{10})\frac{\partial f_2}{\partial x_2}(x_{10},x_{20})\\ +&\frac{1}{2!}(x-x_{10})^2\frac{\partial^2 f_2}{\partial x_2^2}(x_{10},x_{20})+\frac{1}{2!}(x-x_{10})(y-y_{10})\frac{\partial^2 f_2}{\partial x_1\partial x_2}(x_{10},x_{20})\\+&\frac{1}{2!}(x-x_{10})(y-y_{10})\frac{\partial^2 f_2}{\partial x_2\partial x_1}(x_{10},x_{20})+\frac{1}{2!}(y-y_{10})^2\frac{\partial^2 f_2}{\partial x_2^2}(x_{10},x_{20})\end{aligned}$$





$$\begin{aligned} & f_1(x_1,x_2)\approx f_1(x_{10},y_{10})+(x_1-x_{10})\frac{\partial f_1}{\partial x_1}(x_{10},x_{20})+(x_2-x_{20})\frac{\partial f_1}{\partial x_2}(x_{10},x_{20})\\ & f_2(x_1,x_2)\approx f_2(x_{10},y_{10})+(x_1-x_{10})\frac{\partial f_2}{\partial x_1}(x_{10},x_{20})+(x_2-x_{20})\frac{\partial f_2}{\partial x_2}(x_{10},x_{20})\end{aligned}$$



雅可比矩阵

$$\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]$$



$$\left[\begin{matrix}f_1 \\ f_2\end{matrix}\right]\approx \left[\begin{matrix}f_1 (a_0,b_0)\\ f_2(a_0,b_0)\end{matrix}\right]+\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]\left[\begin{matrix}x_1-a_0\\x_2-b_0\end{matrix}\right]$$

黑塞矩阵



$0=\left[\begin{matrix}f_1(a,b) \\ f_2(a,b)\end{matrix}\right]\approx \left[\begin{matrix}f_1 (a_0,b_0)\\ f_2(a_0,b_0)\end{matrix}\right]+\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]\left[\begin{matrix}a-a_0\\b-b_0\end{matrix}\right]$







$$\left[\begin{matrix}a\\b\end{matrix}\right]\approx\left[\begin{matrix}a_0\\b_0\end{matrix}\right]-\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]^{-1}\left[\begin{matrix}f_1(a_0,b_0)\\f_2(a_0,b_0)\end{matrix}\right]$$



$$\left[\begin{matrix}a_n\\b_n\end{matrix}\right]=\left[\begin{matrix}a_{n-1}\\b_{n-1}\end{matrix}\right]-\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]^{-1}\left[\begin{matrix}f_1(a_{n-1},b_{n-1})\\f_2(a_{n-1},b_{n-1})\end{matrix}\right]$$





$$\left[\begin{matrix}a_n\\b_n\end{matrix}\right]=\left[\begin{matrix}a_{n-1}\\b_{n-1}\end{matrix}\right]-\left[\begin{matrix}1 & -1 \\ 2a_{n-1}& 2b_{n-1}\end{matrix}\right]^{-1}\left[\begin{matrix}f_1(a_{n-1},b_{n-1})\\f_2(a_{n-1},b_{n-1})\end{matrix}\right]$$



$$\left[\begin{matrix}a_0\\b_0\end{matrix}\right]=\left[\begin{matrix}0.8\\1.8\end{matrix}\right]$$

$$J=\left[\begin{matrix}1 & -1 \\ 2a_{n-1}& 2b_{n-1}\end{matrix}\right]=\left[\begin{matrix}1 & -1 \\1.6&3.6\end{matrix}\right]$$



$$\left[\begin{matrix}a_1\\b_1\end{matrix}\right]=\left[\begin{matrix}0.8\\1.8\end{matrix}\right]-\left[\begin{matrix}1 & -1 \\ 1.6& 3.6\end{matrix}\right]^{-1}\left[\begin{matrix}0\\-0.12\end{matrix}\right]=\left[\begin{matrix}0.8230769\\ 1.8230769\end{matrix}\right]$$



$$\left[\begin{matrix}a_2\\b_2\end{matrix}\right]=\left[\begin{matrix}0.8230769\\ 1.8230769\end{matrix}\right]-\left[\begin{matrix}1 & -1 \\ 1.6461538 & 3.6461538 \end{matrix}\right]^{-1}\left[\begin{matrix}0\\0.0010651\end{matrix}\right]=\left[\begin{matrix}0.8228757\\ 1.8228757\end{matrix}\right]$$



$$\left[\begin{matrix}a_3\\b_3\end{matrix}\right]=\left[\begin{matrix}0.82287565553230 \\ 1.82287565553230 \end{matrix}\right]$$





$$\left[\begin{matrix}a\\b\end{matrix}\right]=\left[\begin{matrix}\frac{1}{2}(\sqrt7-1)\\ \frac{1}{2}(\sqrt7+1) \end{matrix}\right]$$



