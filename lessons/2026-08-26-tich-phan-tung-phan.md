## Công thức

$$\int u \, dv = uv - \int v \, du$$

Ý tưởng: đổi một tích phân khó thành một tích phân dễ hơn.

## Chọn $u$ thế nào

Ưu tiên theo thứ tự **L – I – A – T – E**, hàm nào đứng trước thì chọn làm $u$:

| Ký hiệu | Loại hàm | Ví dụ |
|---|---|---|
| L | Logarit | $\ln x$ |
| I | Lượng giác ngược | $\arctan x$ |
| A | Đa thức | $x^2$ |
| T | Lượng giác | $\sin x$ |
| E | Mũ | $e^{x}$ |

## Ví dụ

Tính $\displaystyle\int x e^{x}\,dx$.

Theo LIATE: đa thức đứng trước mũ, chọn $u = x$, $dv = e^{x}dx$.

Suy ra $du = dx$, $v = e^{x}$:

$$\int x e^{x}dx = xe^{x} - \int e^{x}dx = xe^{x} - e^{x} + C$$

### Lưu ý

- Đừng quên hằng số $C$.
- Có bài phải dùng từng phần **hai lần** mới ra kết quả.

[[BÀI TẬP]]

[[Câu]] Tính $\displaystyle\int x\ln x\,dx$.
[[Đáp án]] Chọn $u=\ln x$, $dv = x\,dx \Rightarrow du = \dfrac{dx}{x}$, $v = \dfrac{x^{2}}{2}$.
$$\int x\ln x\,dx = \frac{x^{2}}{2}\ln x - \int \frac{x}{2}dx = \frac{x^{2}}{2}\ln x - \frac{x^{2}}{4} + C$$

[[Câu]] Tính $\displaystyle\int_{0}^{\pi} x\sin x\,dx$.
[[Đáp án]] Chọn $u = x$, $dv = \sin x\,dx \Rightarrow v = -\cos x$.
$$\Big[-x\cos x\Big]_{0}^{\pi} + \int_{0}^{\pi}\cos x\,dx = \pi + 0 = \pi$$
