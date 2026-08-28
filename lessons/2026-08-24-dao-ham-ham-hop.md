[[VIDEO]] https://www.youtube.com/watch?v=dQw4w9WgXcQ

## Nhắc lại

Hàm hợp là hàm được tạo bởi hai hàm nối tiếp nhau: đưa $x$ vào $u$, rồi đưa kết quả vào $f$.

$$y = f(u(x))$$

## Công thức

> Nếu $u$ có đạo hàm tại $x$ và $f$ có đạo hàm tại $u(x)$ thì:
> $$y' = f'(u) \cdot u'(x)$$

Cách nhớ: **đạo hàm hàm ngoài giữ nguyên hàm trong, rồi nhân với đạo hàm hàm trong.**

## Các trường hợp thường gặp

| Hàm số | Đạo hàm |
|---|---|
| $u^n$ | $n \cdot u^{n-1} \cdot u'$ |
| $\sqrt{u}$ | $\dfrac{u'}{2\sqrt{u}}$ |
| $\sin u$ | $u' \cdot \cos u$ |
| $e^{u}$ | $u' \cdot e^{u}$ |
| $\ln u$ | $\dfrac{u'}{u}$ |

## Ví dụ

Tính đạo hàm của $y = \sin(3x^2 + 1)$.

Đặt $u = 3x^2 + 1 \Rightarrow u' = 6x$. Khi đó:

$$y' = u' \cdot \cos u = 6x\cos(3x^2+1)$$

### Lỗi hay mắc

- Quên nhân $u'$ ở cuối.
- Với $u^n$, đạo hàm số mũ thay vì giữ $n$ ở trước.

[[BÀI TẬP]]

[[Câu]] Tính đạo hàm của $y = (2x+5)^{4}$.
[[Đáp án]] Đặt $u = 2x+5$, $u' = 2$.
$$y' = 4u^{3}\cdot u' = 8(2x+5)^{3}$$

[[Câu]] Tính đạo hàm của $y = \sqrt{x^{2}+1}$.
[[Đáp án]] Với $u = x^{2}+1$, $u' = 2x$:
$$y' = \frac{u'}{2\sqrt{u}} = \frac{x}{\sqrt{x^{2}+1}}$$

[[Câu]] Tính đạo hàm của $y = e^{\sin x}$.
[[Đáp án]] $u = \sin x$, $u' = \cos x$, nên $y' = \cos x \cdot e^{\sin x}$.
