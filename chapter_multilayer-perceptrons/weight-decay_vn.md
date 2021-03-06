<!-- ===================== Bắt đầu dịch Phần 1 ===================== -->
<!-- ========================================= REVISE PHẦN 1 - BẮT ĐẦU =================================== -->

<!--
# Weight Decay
-->

# Phân rã trọng số
:label:`sec_weight_decay`

<!--
Now that we have characterized the problem of overfitting, we can introduce some standard techniques for regularizing models.
Recall that we can always mitigate overfitting by going out and collecting more training data, that can be costly, time consuming, or entirely out of our control, making it impossible in the short run.
For now, we can assume that we already have as much high-quality data as our resources permit and focus on regularization techniques.
-->

Đến giờ chúng ta đã mô tả vấn đề quá khớp, ta sẽ tìm hiểu thêm một vài kỹ thuật chuẩn trong việc điều chuẩn mô hình.
Nhắc lại rằng chúng ta luôn có thể tránh được hiện tượng quá khớp bằng cách thu thập thêm nhiều dữ liệu huấn luyện, nhưng việc này có thể rất tốn kém, tốn thời gian, hoặc có thể nằm ngoài khả năng của ta, nên trong ngắn hạn, nó là bất khả thi.
Hiện tại, chúng ta có thể giả sử rằng ta đã có được lượng dữ liệu chất lượng cao nhiều nhất có thể và tập trung vào các kỹ thuật điều chuẩn.

<!--
Recall that in our polynomial curve-fitting example (:numref:`sec_model_selection`) we could limit our model's capacity simply by tweaking the degree of the fitted polynomial.
Indeed, limiting the number of features is a popular technique to avoid overfitting.
However, simply tossing aside features can be too blunt a hammer for the job.
Sticking with the polynomial curve-fitting example, consider what might happen with high-dimensional inputs.
The natural extensions of polynomials to multivariate data are called *monomials*, which are simply products of powers of variables.
The degree of a monomial is the sum of the powers.
For example, $x_1^2 x_2$, and $x_3 x_5^2$ are both monomials of degree $3$.
-->

Nhắc lại rằng trong ví dụ về việc khớp đường cong đa thức (:numref:`sec_model_selection`), chúng ta có thể giới hạn độ phức tạp của mô hình đơn giản bằng cách chỉnh số bậc của đa thức.
Đúng như vậy, giới hạn số đặc trưng đầu vào là một kỹ thuật phổ biến để tránh hiện tượng quá khớp.
Tuy nhiên, việc đơn thuần loại bỏ các đặc trưng có thể hơi quá mạnh tay.
Quay lại với ví dụ về việc khớp đường cong đa thức, hãy xét chuyện gì sẽ xảy ra với đầu vào nhiều chiều.
Ta mở rộng đa thức cho dữ liệu đa biến bằng việc thêm các *đơn thức*, thứ đơn thuần chỉ là tích của lũy thừa các biến.
Bậc của một đơn thức là tổng của các số mũ.
Ví dụ, $x_1^2 x_2$, và $x_3 x_5^2$ đều là các đơn thức bậc $3$.

<!--
Note that the number of terms with degree $d$ blows up rapidly as $d$ grows larger.
Given $k$ variables, the number of monomials of degree $d$ is ${k - 1 + d} \choose {k - 1}$.
Even small changes in degree, say, from $2$ to $3$ dramatically increase the complexity of our model.
Thus we often need a more fine-grained tool for adjusting function complexity.
-->

Lưu ý rằng số lượng phần tử bậc $d$ tăng cực kỳ nhanh khi $d$ tăng.
Cho $k$ biến, số lượng các đơn thức bậc $d$ là ${k - 1 + d} \choose {k - 1}$.
Thậm chí chỉ một thay đổi nhỏ về số bậc, ví dụ từ $2$ lên $3$ cũng sẽ tăng độ phức tạp của mô hình một cách chóng mặt.
Do vậy, chúng ta cần có một công cụ tốt hơn để điều chỉnh độ phức tạp của hàm số.

<!-- ===================== Kết thúc dịch Phần 1 ===================== -->

<!-- ===================== Bắt đầu dịch Phần 2 ===================== -->

<!--
## Squared Norm Regularization
-->

## Điều chuẩn Chuẩn Bình phương

<!--
*Weight decay* (commonly called *L2* regularization), might be the most widely-used technique for regularizing parametric machine learning models.
The technique is motivated by the basic intuition that among all functions $f$,
the function $f = 0$ (assigning the value $0$ to all inputs) is in some sense the *simplest* and that we can measure the complexity of a function by its distance from zero.
But how precisely should we measure the distance between a function and zero?
There is no single right answer.
In fact, entire branches of mathematics, including parts of functional analysis and the theory of Banach spaces are devoted to answering this issue.
-->

*Phân rã trọng số* (thông thường gọi là điều chuẩn *L2*), có thể là kỹ thuật được sử dụng rộng rãi nhất để điều chuẩn các mô hình học máy có tham số.
Kỹ thuật này dựa trên một quan sát cơ bản: trong tất cả các hàm $f$, hàm $f = 0$ (gán giá trị $0$ cho tất cả các đầu vào) có lẽ là hàm *đơn giản nhất* và ta có thể đo độ phức tạp của hàm số bằng khoảng cách giữa nó và giá trị không.
Nhưng cụ thể thì ta đo khoảng cách giữa một hàm số và số không như thế nào?
Không chỉ có duy nhất một câu trả lời đúng.
Trong thực tế, có nguyên cả các nhánh toán học gồm một phần giải tích hàm và lý thuyết không gian Banach đều tập trung trả lời câu hỏi này.

<!--
One simple interpretation might be to measure the complexity of a linear function $f(\mathbf{x}) = \mathbf{w}^\top \mathbf{x}$ by some norm of its weight vector, e.g., $|| \mathbf{w} ||^2$.
The most common method for ensuring a small weight vector is to add its norm as a penalty term to the problem of minimizing the loss.
Thus we replace our original objective, *minimize the prediction loss on the training labels*, with new objective, *minimize the sum of the prediction loss and the penalty term*.
Now, if our weight vector grows too large, our learning algorithm might *focus* on minimizing the weight norm $|| \mathbf{w} ||^2$ versus minimizing the training error.
That is exactly what we want.
To illustrate things in code, let's revive our previous example from :numref:`sec_linear_regression` for linear regression.
There, our loss was given by
-->

Một cách đơn giản để đo độ phức tạp của hàm tuyến tính $f(\mathbf{x}) = \mathbf{w}^\top \mathbf{x}$ là dựa vào chuẩn của vector trọng số, ví dụ như $|| \mathbf{w} ||^2$.
Phương pháp phổ biến nhất để đảm bảo rằng ta sẽ có một vector trọng số nhỏ là thêm chuẩn của nó (đóng vai trò như một thành phần phạt) vào bài toán tối thiểu hóa hàm mất mát.
Do đó, ta thay thế mục tiêu ban đầu *tối thiểu hóa hàm mất mát dự đoán trên nhãn huấn luyện*, bằng mục tiêu mới, *tối thiểu hóa tổng của hàm mất mát dự đoán và thành phần phạt*.
Bây giờ, nếu vector trọng số tăng quá lớn, thuật toán học sẽ *tập trung* giảm thiểu chuẩn trọng số $|| \mathbf{w} ||^2$ hơn là giảm thiểu lỗi huấn luyện.
Đó chính xác là những gì ta muốn.
Để minh họa mọi thứ bằng mã, hãy xét lại ví dụ hồi quy tuyến tính trong :numref:`sec_linear_regression`.
Ở đó, hàm mất mát được định nghĩa như sau:

$$l(\mathbf{w}, b) = \frac{1}{n}\sum_{i=1}^n \frac{1}{2}\left(\mathbf{w}^\top \mathbf{x}^{(i)} + b - y^{(i)}\right)^2.$$

<!--
Recall that $\mathbf{x}^{(i)}$ are the observations, $y^{(i)}$ are labels, and $(\mathbf{w}, b)$ are the weight and bias parameters respectively.
To penalize the size of the weight vector, we must somehow add $|| \mathbf{w} ||^2$ to the loss function, but how should the model trade off the standard loss for this new additive penalty?
In practice, we characterize this tradeoff via the *regularization constant* $\lambda > 0$, a non-negative hyperparameter that we fit using validation data:
-->

Nhắc lại $\mathbf{x}^{(i)}$ là các quan sát, $y^{(i)}$ là các nhãn và $(\mathbf{w}, b)$ lần lượt là trọng số và hệ số điều chỉnh.
Để phạt kích thước của vector trọng số, bằng cách nào đó ta phải cộng thêm $||mathbf{w}||^2$ vào hàm mất mát, nhưng mô hình nên cân bằng hàm mát mát thông thường với thành phần phạt mới này như thế nào?
Trong thực tế, ta mô tả sự cân bằng này thông qua *hằng số điều chuẩn* $\lambda > 0$, một siêu tham số không âm mà ta khớp được bằng cách sử dụng dữ liệu kiểm định:

$$l(\mathbf{w}, b) + \frac{\lambda}{2} \|\mathbf{w}\|^2.$$

<!--
For $\lambda = 0$, we recover our original loss function.
For $\lambda > 0$, we restrict the size of $|| \mathbf{w} ||$.
The astute reader might wonder why we work with the squared norm and not the standard norm (i.e., the Euclidean distance).
We do this for computational convenience.
By squaring the L2 norm, we remove the square root, leaving the sum of squares of each component of the weight vector.
This makes the derivative of the penalty easy to compute (the sum of derivatives equals the derivative of the sum).
-->

Với $\lambda = 0$, ta thu lại được hàm mất mát gốc.
Với $\lambda > 0$, ta hạn chế kích thước của $|| \mathbf{w} ||$.
Bạn đọc nào tinh ý có thể tự hỏi tại sao ta dùng chuẩn bình phương chứ không phải chuẩn thông thường (nghĩa là khoảng cách Euclide).
Ta làm điều này để thuận tiện cho việc tính toán.
Bằng cách bình phương chuẩn L2, ta khử được căn bậc hai, chỉ còn lại tổng bình phương từng thành phần của vector trọng số.
Điều này giúp việc tính đạo hàm của thành phần phạt dễ dàng hơn (tổng các đạo hàm bằng đạo hàm của tổng).

<!--
Moreover, you might ask why we work with the L2 norm in the first place and not, say, the L1 norm.
-->

Hơn nữa, có thể bạn sẽ hỏi tại sao ta lại dùng chuẩn L2 ngay từ đầu chứ không phải là chuẩn L1.

<!--
In fact, other choices are valid and popular throughout statistics.
While L2-regularized linear models constitute the classic *ridge regression* algorithm, L1-regularized linear regression is 
a similarly fundamental model in statistics (popularly known as *lasso regression*).
-->

Trong thực tế, các lựa chọn khác đều hợp lệ và phổ biến trong thống kê.
Trong khi các mô hình tuyến tính được điều chuẩn-L2 tạo thành thuật toán *hồi quy ridge*, hồi quy tuyến tính được điều chuẩn-L1 cũng là một mô hình cơ bản trong thống kê (thường được gọi là *hồi quy lasso*).

<!--
More generally, the $\ell_2$ is just one among an infinite class of norms call p-norms, many of which you might encounter in the future.
In general, for some number $p$, the $\ell_p$ norm is defined as
-->

Một cách tổng quát, chuẩn $\ell_2$ chỉ là một trong vô số các chuẩn được gọi chung là chuẩn-p, và sau này bạn sẽ có thể gặp một vài chuẩn như vậy.
Thông thường, với một số $p$, chuẩn$\ell_p$ được định nghĩa là

$$\|\mathbf{w}\|_p^p := \sum_{i=1}^d |w_i|^p.$$

<!-- ===================== Kết thúc dịch Phần 2 ===================== -->

<!-- ===================== Bắt đầu dịch Phần 3 ===================== -->

<!--
One reason to work with the L2 norm is that it places and outsize penalty on large components of the weight vector.
This biases our learning algorithm towards models that distribute weight evenly across a larger number of features.
In practice, this might make them more robust to measurement error in a single variable.
By contrast, L1 penalties lead to models that concentrate weight on a small set of features, which may be desirable for other reasons.
-->

Một lý do để sử dụng chuẩn L2 là vì nó phạt rất nặng những thành phần lớn của vector trọng số.
Việc này khiến thuật toán học thiên vị các mô hình có trọng số được phân bổ đồng đều cho một số lượng lớn các đặc trưng.
Trong thực tế, điều này có thể giúp làm giảm ảnh hưởng do lỗi đo lường của từng biến đơn lẻ.
Ngược lại, các hình phạt của L1 hướng đến các mô hình mà trọng số chỉ tập trung vào một số lượng nhỏ các đặc trưng, và ta có thể muốn điều này vì một vài lý do khác. 

<!--
The stochastic gradient descent updates for L2-regularized regression follow:
-->

Việc cập nhật hạ gradient ngẫu nhiên cho hồi quy được chuẩn hóa L2 được tiến hành như sau:

$$
\begin{aligned}
\mathbf{w} & \leftarrow \left(1- \eta\lambda \right) \mathbf{w} - \frac{\eta}{|\mathcal{B}|} \sum_{i \in \mathcal{B}} \mathbf{x}^{(i)} \left(\mathbf{w}^\top \mathbf{x}^{(i)} + b - y^{(i)}\right),
\end{aligned}
$$

<!--
As before, we update $\mathbf{w}$ based on the amount by which our estimate differs from the observation.
However, we also shrink the size of $\mathbf{w}$ towards $0$.
That is why the method is sometimes called "weight decay": given the penalty term alone, our optimization algorithm *decays* the weight at each step of training.
In contrast to feature selection, weight decay offers us a continuous mechanism for adjusting the complexity of $f$.
Small values of $\lambda$ correspond to unconstrained $\mathbf{w}$, whereas large values of $\lambda$ constrain $\mathbf{w}$ considerably.
Whether we include a corresponding bias penalty $b^2$ can vary across implementations, and may vary across layers of a neural network.
Often, we do not regularize the bias term of a network's output layer.
-->

Như trước đây, ta cập nhật $\mathbf{w}$ dựa trên hiệu của giá trị ước lượng và giá trị quan sát được.
Tuy nhiên, ta cũng sẽ thu nhỏ độ lớn của $\mathbf{w}$ về $0$.
Đó là lý do tại sao phương pháp này còn đôi khi được gọi là "phân rã trọng số": nếu chỉ có số hạng phạt, thuật toán tối ưu sẽ *phân rã* trọng số ở từng bước huấn luyện.
Trái ngược với lựa chọn đặc trưng, phân rã trọng số cho ta một cơ chế liên tục cho việc thay đổi độ phức tạp của $f$.
Các giá trị nhỏ của $\lambda$ tương ứng với việc $\mathbf{w}$ không bị ràng buộc, trong khi các giá trị lớn của $\lambda$ ràng buộc $\mathbf{w}$ một cách đáng kể.
Còn việc có nên thêm lượng phạt cho hệ số điều chỉnh tương ứng $b^2$ hay không thì tùy thuộc ở mỗi cách lập trình, và có thể khác nhau giữa các tầng của mạng nơ-ron.
Thông thường, ta không điều chuẩn hóa hệ số điều chỉnh của tầng đầu ra của mạng.


<!-- ========================================= REVISE PHẦN 1 - KẾT THÚC ===================================-->

<!-- ========================================= REVISE PHẦN 2 - BẮT ĐẦU ===================================-->

<!--
## High-Dimensional Linear Regression
-->

## Hồi quy Tuyến tính nhiều chiều

<!--
We can illustrate the benefits of weight decay over feature selection through a simple synthetic example.
First, we generate some data as before
-->

Ta có thể minh họa các ưu điểm của suy giảm trọng số so với lựa chọn đặc trưng thông qua một ví dụ đơn giản với dữ liệu tự tạo. 
Đầu tiên, ta tạo ra dữ liệu giống như trước đây

<!--
$$y = 0.05 + \sum_{i = 1}^d 0.01 x_i + \epsilon \text{ where }
\epsilon \sim \mathcal{N}(0, 0.01).$$
-->

$$y = 0.05 + \sum_{i = 1}^d 0.01 x_i + \epsilon \text{ với }
\epsilon \sim \mathcal{N}(0, 0.01).$$

<!--
choosing our label to be a linear function of our inputs, corrupted by Gaussian noise with zero mean and variance 0.01.
To make the effects of overfitting pronounced, we can increase the dimensinoality of our problem to $d = 200$
and work with a small training set containing only 20 example.
-->

lựa chọn nhãn là một hàm tuyến tính của các đầu vào, bị biến dạng bởi nhiễu Gauss với trung bình bằng không và phương sai bằng 0.01. 
Để làm cho hiệu ứng của việc quá khớp trở nên rõ ràng, ta có thể tăng số chiều của bài toán lên $d = 200$ và làm việc với một tập huấn luyện nhỏ bao gồm chỉ 20 mẫu.  

```{.python .input  n=1}
%matplotlib inline
import d2l
from mxnet import autograd, gluon, init, np, npx
from mxnet.gluon import nn
npx.set_np()

n_train, n_test, num_inputs, batch_size = 20, 100, 200, 5
true_w, true_b = np.ones((num_inputs, 1)) * 0.01, 0.05
train_data = d2l.synthetic_data(true_w, true_b, n_train)
train_iter = d2l.load_array(train_data, batch_size)
test_data = d2l.synthetic_data(true_w, true_b, n_test)
test_iter = d2l.load_array(test_data, batch_size, is_train=False)
```

<!-- ===================== Kết thúc dịch Phần 3 ===================== -->

<!-- ===================== Bắt đầu dịch Phần 4 ===================== -->

<!--
## Implementation from Scratch
-->

## Lập trình từ đầu

<!--
Next, we will implement weight decay from scratch, simply by adding the squared $\ell_2$ penalty to the original target function.
-->

Tiếp theo, chúng ta sẽ lập trình suy giảm trọng số từ đầu, chỉ đơn giản bằng cách cộng thêm bình phương lượng phạt $\ell_2$ vào hàm mục tiêu ban đầu.

<!--
### Initializing Model Parameters
-->

### Khởi tạo Tham số Mô hình

<!--
First, we will define a function to randomly initialize our model parameters and run `attach_grad` on each to allocate memory for the gradients we will calculate.
-->

Đầu tiên, chúng ta khai báo một hàm để khởi tạo tham số cho mô hình một cách ngẫu nhiên và chạy `attach_grad` với mỗi tham số để cấp phát bộ nhớ cho gradient mà ta sẽ tính toán. 

```{.python .input  n=2}
def init_params():
    w = np.random.normal(scale=1, size=(num_inputs, 1))
    b = np.zeros(1)
    w.attach_grad()
    b.attach_grad()
    return [w, b]
```

<!--
### Defining $\ell_2$ Norm Penalty
-->

### Định nghĩa Lượng phạt Chuẩn $\ell_2$

<!--
Perhaps the most convenient way to implement this penalty is to square all terms in place and sum them up.
We divide by $2$ by convention, (when we take the derivative of a quadratic function, the $2$ and $1/2$ cancel out, ensuring that the expression for the update looks nice and simple).
-->

Có lẽ cách thuận tiện nhất để lập trình lượng phạt này là bình phương tất cả các phần tử ngay tại chỗ và cộng chúng lại với nhau. 
Ta đem chia với $2$ theo quy ước (khi ta tính đạo hàm của hàm bậc hai, $2$ và $1/2$ sẽ loại trừ nhau, đảm bảo biểu thức cập nhật trông đơn giản, dễ nhìn). 

```{.python .input  n=3}
def l2_penalty(w):
    return (w**2).sum() / 2
```

<!--
### Defining the Train and Test Functions
-->

### Định nghĩa hàm Huấn luyện và Kiểm tra

<!--
The following code fits a model on the training set and evaluates it on the test set.
The linear network and the squared loss have not changed since the previous chapter, so we will just import them via `d2l.linreg` and `d2l.squared_loss`.
The only change here is that our loss now includes the penalty term.
-->

Đoạn mã nguồn sau thực hiện việc khớp mô hình trên tập huấn luyện và đánh giá nó trên tập kiểm tra. 
Mạng tuyến tính và hàm mất mát bình phương không thay đổi gì so với chương trước, vì vậy ta chỉ cần nhập chúng qua `d2l.linreg` và `d2l.squared_loss`. 
Thay đổi duy nhất ở đây là hàm mất mát có thêm lượng phạt. 

```{.python .input  n=4}
def train(lambd):
    w, b = init_params()
    net, loss = lambda X: d2l.linreg(X, w, b), d2l.squared_loss
    num_epochs, lr = 100, 0.003
    animator = d2l.Animator(xlabel='epochs', ylabel='loss', yscale='log',
                            xlim=[1, num_epochs], legend=['train', 'test'])
    for epoch in range(1, num_epochs + 1):
        for X, y in train_iter:
            with autograd.record():
                # The L2 norm penalty term has been added, and broadcasting
                # makes l2_penalty(w) a vector whose length is batch_size
                l = loss(net(X), y) + lambd * l2_penalty(w)
            l.backward()
            d2l.sgd([w, b], lr, batch_size)
        if epoch % 5 == 0:
            animator.add(epoch, (d2l.evaluate_loss(net, train_iter, loss),
                                 d2l.evaluate_loss(net, test_iter, loss)))
    print('l1 norm of w:', np.abs(w).sum())
```

<!--
### Training without Regularization
-->

### Huấn luyện không Điều chuẩn

<!--
We now run this code with `lambd = 0`, disabling weight decay.
Note that we overfit badly, decreasing the training error but not the test error---a textook case of overfitting.
-->

Giờ chúng ta sẽ chạy đoạn mã này với `lambd = 0`, vô hiệu hóa suy giảm trọng số. 
Hãy để ý tới việc quá khớp nặng, lỗi huấn luyện giảm nhưng lỗi kiểm tra thì không---một trường hợp điển hình của hiện tượng quá khớp. 

```{.python .input  n=5}
train(lambd=0)
```

<!-- ===================== Kết thúc dịch Phần 4 ===================== -->

<!-- ===================== Bắt đầu dịch Phần 5 ===================== -->

<!-- ========================================= REVISE PHẦN 2 - KẾT THÚC ===================================-->

<!-- ========================================= REVISE PHẦN 3 - BẮT ĐẦU ===================================-->

<!--
### Using Weight Decay
-->

### Sử dụng Suy giảm Trọng số

<!--
Below, we run with substantial weight decay.
Note that the training error increases but the test error decreases.
This is precisely the effect we expect from regularization.
As an exercise, you might want to check that the $\ell_2$ norm of the weights $\mathbf{w}$ has actually decreased.
-->

Bên dưới, chúng ta chạy với suy giảm trọng số lớn.
Cần chú ý rằng lỗi huấn luyện tăng nhưng lỗi kiểm định giảm.
Đây chính xác là hiệu ứng mà chúng ta mong đợi từ điều chuẩn.
Xem như một bài tập, bạn có thể kiểm tra rằng chuẩn $\ell_2$ của các trọng số $\mathbf{w}$ thực sự giảm.

```{.python .input  n=6}
train(lambd=3)
```

<!--
## Concise Implementation
-->

## Cách lập trình súc tích

<!--
Because weight decay is ubiquitous in neural network optimization,
Gluon makes it especially convenient, integrating weight decay into the optimization algorithm itself for easy use in combination with any loss function.
Moreover, this integration serves a computational benefit, allowing implementation tricks to add weight decay to the algorithm, without any additional computational overhead.
Since the weight decay portion of the update depends only on the current value of each parameter, and the optimizer must to touch each parameter once anyway.
-->

Bởi vì suy giảm trọng số có ở khắp mọi nơi trong tối ưu mạng nơ-ron,
Gluon làm cho việc áp dụng kĩ thuật này trở nên thuận tiện, bằng cách tích hợp suy giảm trọng số vào chính giải thuật tối ưu để có thể kết hợp với bất kì hàm mất mát nào.
Hơn nữa, việc tích hợp này cũng đem lại lợi ích tính toán, cho phép ta sử dụng các thủ thuật lập trình để thêm suy giảm trọng số vào thuật toán mà không làm tăng tổng chi phí tính toán.
Nguyên nhân là do tại mỗi bước cập nhật, phần suy giảm trọng số chỉ phụ thuộc vào giá trị hiện tại của mỗi tham số và bộ tối ưu hoá đằng nào thì cũng phải đụng tới mỗi tham số một lần.

<!--
In the following code, we specify the weight decay hyperparameter directly through `wd` when instantiating our `Trainer`.
By default, Gluon decays both weights and biases simultaneously.
Note that the hyperparameter `wd` will be multiplied by `wd_mult` when updating model parameters.
Thus, if we set `wd_mult` to $0$, the bias parameter $b$ will not decay.
-->

Trong đoạn mã nguồn sau đây, chúng ta chỉ định trực tiếp siêu tham số suy giảm trọng số thông qua giá trị `wd` khi khởi tạo `Trainer`.
Theo mặc định, Gluon suy giảm đồng thời cả trọng số và hệ số điều chỉnh.
Cần chú ý rằng siêu tham số `wd` sẽ được nhân lên với `wd_mult` khi cập nhật các tham số mô hình.
Như vậy, nếu chúng ta thiết lập `wd_mult` bằng $0$, tham số hệ số điều chỉnh $b$ sẽ không suy giảm.

```{.python .input  n=7}
def train_gluon(wd):
    net = nn.Sequential()
    net.add(nn.Dense(1))
    net.initialize(init.Normal(sigma=1))
    loss = gluon.loss.L2Loss()
    num_epochs, lr = 100, 0.003
    trainer = gluon.Trainer(net.collect_params(), 'sgd',
                            {'learning_rate': lr, 'wd': wd})
    # The bias parameter has not decayed. Bias names generally end with "bias"
    net.collect_params('.*bias').setattr('wd_mult', 0)

    animator = d2l.Animator(xlabel='epochs', ylabel='loss', yscale='log',
                            xlim=[1, num_epochs], legend=['train', 'test'])
    for epoch in range(1, num_epochs+1):
        for X, y in train_iter:
            with autograd.record():
                l = loss(net(X), y)
            l.backward()
            trainer.step(batch_size)
        if epoch % 5 == 0:
            animator.add(epoch, (d2l.evaluate_loss(net, train_iter, loss),
                                 d2l.evaluate_loss(net, test_iter, loss)))
    print('L1 norm of w:', np.abs(net[0].weight.data()).sum())
```

<!--
The plots look identical to those when we implemented weight decay from scratch.
However, they run appreciably faster and are easier to implement, a benefit that will become more pronounced for large problems.
-->

Các đồ thị này nhìn giống hệt với những đồ thị khi chúng ta lập trình suy giảm trọng số từ đầu.
Tuy nhiên, chúng chạy nhanh hơn rõ rệt và dễ lập trình hơn, một lợi ích mà sẽ dễ nhận thấy hơn với các bài toán lớn.

```{.python .input  n=8}
train_gluon(0)
```

```{.python .input  n=9}
train_gluon(3)
```

<!--
So far, we only touched upon one notion of what constitutes a simple *linear* function.
Moreover, what constitutes a simple *nonlinear* function, can be an even more complex question.
For instance, [Reproducing Kernel Hilbert Spaces (RKHS)](https://en.wikipedia.org/wiki/Reproducing_kernel_Hilbert_space) allow one to apply tools introduced for linear functions in a nonlinear context.
Unfortunately, RKHS-based algorithms tend to scale purely to large, high-dimensional data.
In this book we will default to the simple heuristic of applying weight decay on all layers of a deep network.
-->

<!-- ===================== Kết thúc dịch Phần 5 ===================== -->

<!-- ===================== Bắt đầu dịch Phần 6 ===================== -->

Tới giờ, chúng ta mới chỉ đề cập đến một ý niệm về những gì cấu thành nên một hàm *tuyến tính* đơn giản.
Hơn nữa, những gì cấu thành nên một hàm *phi tuyến* đơn giản lại là một câu hỏi thậm chí còn phức tạp hơn.
Ví dụ, [Tái tạo các không gian kernel Hilbert (RKHS)](https://en.wikipedia.org/wiki/Reproducing_kernel_Hilbert_space) cho phép chúng ta áp dụng các công cụ được giới thiệu cho các hàm tuyến tính trong một ngữ cảnh phi tuyến.
Không may mắn thay, các giải thuật dựa vào RKHS thường không thể nhân rộng và hoạt động hiệu quả trên dữ liệu lớn, đa chiều.
Dựa trên một thực nghiệm đơn giản, trong quyển sách này chúng ta mặc định sẽ áp dụng phương pháp suy giảm trọng số cho tất cả các tầng của mạng học sâu.

<!--
## Summary
-->

## Tóm tắt

<!--
* Regularization is a common method for dealing with overfitting. It adds a penalty term to the loss function on the training set to reduce the complexity of the learned model.
* One particular choice for keeping the model simple is weight decay using an $\ell_2$ penalty. This leads to weight decay in the update steps of the learning algorithm.
* Gluon provides automatic weight decay functionality in the optimizer by setting the hyperparameter `wd`.
* You can have different optimizers within the same training loop, e.g., for different sets of parameters.
-->

* Điều chuẩn là một phương pháp phổ biến để giải quyết quá khớp.
Nó thêm một lượng phạt vào hàm mất mát trong tập huấn luyện để giảm thiểu độ phức tạp của mô hình học.
* Một cách cụ thể để giữ mô hình đơn giản là suy giảm trọng số sử dụng lượng phạt $\ell_2$. Điều này dẫn đến giá trị trọng số sẽ suy giảm trong các bước cập nhật của giải thuật học.
* Gluon cung cấp tính năng phân rã trọng số tự động trong bộ tối ưu hoá bằng cách thiết lập siêu tham số `wd`.
* Bạn có thể dùng những bộ tối ưu hoá khác nhau trong cùng một vòng lặp huấn luyện, chẳng hạn, cho các tập tham số khác nhau. 
<!--
## Exercises
-->

## Bài tập

<!--
1. Experiment with the value of $\lambda$ in the estimation problem in this page. Plot training and test accuracy as a function of $\lambda$. What do you observe?
2. Use a validation set to find the optimal value of $\lambda$. Is it really the optimal value? Does this matter?
3. What would the update equations look like if instead of $\|\mathbf{w}\|^2$ we used $\sum_i |w_i|$ as our penalty of choice (this is called $\ell_1$ regularization).
4. We know that $\|\mathbf{w}\|^2 = \mathbf{w}^\top \mathbf{w}$. 
Can you find a similar equation for matrices (mathematicians call this the [Frobenius norm](https://en.wikipedia.org/wiki/Matrix_norm#Frobenius_norm))?
5. Review the relationship between training error and generalization error. 
In addition to weight decay, increased training, and the use of a model of suitable complexity, what other ways can you think of to deal with overfitting?
6. In Bayesian statistics we use the product of prior and likelihood to arrive at a posterior via $P(w \mid x) \propto P(x \mid w) P(w)$. How can you identify $P(w)$ with regularization?
-->

1. Thí nghiệm với giá trị của $\lambda$ trong bài toán ước lượng ở trang này.
Vẽ đồ thị độ chính xác của tập huấn luyện và tập kiểm tra như một hàm số của $\lambda$.
Bạn quan sát được những gì?
2. Sử dụng tập kiểm định để tìm giá trị tối ưu của $\lambda$.
Nó có thật sự là giá trị tối ưu hay không?
Điều này có quan trọng không?
3. Các phương trình cập nhật sẽ có dạng như thế nào nếu thay vì $\|\mathbf{w}\|^2$ chúng ta sử dụng lượng phạt $\sum_i |w_i|$ (được gọi là điều chuẩn $\ell_1$).
4. Chúng ta đã biết rằng $\|\mathbf{w}\|^2 = \mathbf{w}^\top \mathbf{w}$.
Bạn có thể tìm một phương trình tương tự cho các ma trận (các nhà toán học gọi nó là [chuẩn Frobenius](https://en.wikipedia.org/wiki/Matrix_norm#Frobenius_norm)) hay không?
5. Ôn lại mối quan hệ giữa lỗi huấn luyện và lỗi khái quát.
Bên cạnh suy giảm trọng số, tăng cường việc huấn luyện và sử dụng một mô hình có độ phức tạp phù hợp, bạn có thể nghĩ ra cách nào khác để giải quyết quá khớp không?
6. Trong thống kê Bayesian chúng ta sử dụng tích của tiên nghiệm và hàm hợp lý để suy luận ra hậu nghiệm thông qua $P(w \mid x) \propto P(x \mid w) P(w)$. Bạn có thể nhận ra mối liên hệ giữa $P(w)$ với điều chuẩn hay không?

<!-- ===================== Kết thúc dịch Phần 6 ===================== -->

<!-- ========================================= REVISE PHẦN 3 - KẾT THÚC ===================================-->

<!--
## [Discussions](https://discuss.mxnet.io/t/2342)
-->

## Thảo luận
* [Tiếng Anh](https://discuss.mxnet.io/t/2342)
* [Tiếng Việt](https://forum.machinelearningcoban.com/c/d2l)


## Những người thực hiện
Bản dịch trong trang này được thực hiện bởi:
<!--
Tác giả của mỗi Pull Request điền tên mình và tên những người review mà bạn thấy
hữu ích vào từng phần tương ứng. Mỗi dòng một tên, bắt đầu bằng dấu `*`.

Lưu ý:
* Nếu reviewer không cung cấp tên, bạn có thể dùng tên tài khoản GitHub của họ
với dấu `@` ở đầu. Ví dụ: @aivivn.

* Tên đầy đủ của các reviewer có thể được tìm thấy tại https://github.com/aivivn/d2l-vn/blob/master/docs/contributors_info.md.
-->

* Đoàn Võ Duy Thanh
<!-- Phần 1 -->
* Nguyễn Văn Tâm
* Vũ Hữu Tiệp

<!-- Phần 2 -->
* Lý Phi Long
* Lê Khắc Hồng Phúc
* Phạm Minh Đức

<!-- Phần 3 -->
* Nguyễn Duy Du
* Phạm Minh Đức

<!-- Phần 4 -->
* Nguyễn Văn Tâm
* Vũ Hữu Tiệp
* Phạm Minh Đức

<!-- Phần 5 -->
* Lê Cao Thăng
* Lê Khắc Hồng Phúc

<!-- Phần 6 -->
* Lê Cao Thăng
* Lê Khắc Hồng Phúc
