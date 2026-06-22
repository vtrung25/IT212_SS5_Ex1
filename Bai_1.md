# BÀI 1: PHÂN TÍCH & LỰA CHỌN (KỸ THUẬT PROMPT SUY LUẬN THEO CÁC BƯỚC - CHAIN-OF-THOUGHT)

## 1. Đáp án lựa chọn
**Phương án tối ưu nhất là Phương án B.**

---

## 2. Phân tích chi tiết tại sao Phương án B là tối ưu nhất

Phương án B là một ví dụ mẫu mực áp dụng kỹ thuật **Chain-of-Thought (CoT - Chuỗi suy luận)** kết hợp đầy đủ **5 thành phần cốt lõi của một Prompt chuyên nghiệp**:

### A. Áp dụng kỹ thuật Chain-of-Thought (CoT)
* **Nguyên lý:** Yêu cầu AI *"Đừng viết code ngay. Hãy phân tích và suy luận từng bước sau ra màn hình trước..."*. Bằng cách ép AI giải thích các bước tính toán trung gian, ta giảm thiểu đáng kể lỗi suy diễn vội vã (hallucination) thường gặp ở các mô hình ngôn ngữ lớn (LLM).
* **Kiểm chứng thực tế (Dry-run):** Yêu cầu chạy tay (Dry-run) với một ví dụ cụ thể (30 triệu VND, 1 người phụ thuộc, không bảo hiểm). Khi AI tự tay thực hiện tính toán bằng chữ trước, nó sẽ tạo ra một "mỏ neo logic". Đoạn code sinh ra sau đó sẽ bắt buộc phải tuân theo logic toán học đã được kiểm chứng này, đảm bảo độ chính xác tuyệt đối.

### B. Cấu trúc 5 thành phần của Prompt trong Phương án B
1. **Vai trò (Role/Persona):** `"Hãy đóng vai trò là một Java Developer chuyên nghiệp kiêm chuyên gia tài chính."` -> Định hướng AI sử dụng phong cách viết code chuyên nghiệp chuẩn tài chính (chính xác, an toàn, dễ bảo trì).
2. **Bối cảnh (Context):** `"Nhiệm vụ của bạn là viết một class Java tính thuế thu nhập cá nhân (TNCN) lũy tiến dựa trên thu nhập chịu thuế."` -> Cung cấp thông tin nghiệp vụ cần giải quyết.
3. **Ràng buộc (Constraints):** `"Sử dụng Java 17, kiểu dữ liệu BigDecimal để tránh sai số làm tròn tiền tệ. Các bậc thuế lũy tiến giả định:..."` -> Ràng buộc công nghệ cực kỳ quan trọng đối với dữ liệu tiền tệ (không dùng `double` hay `float`).
4. **Dữ liệu đầu vào (Input Data):** Biểu thuế lũy tiến giả định gồm 7 bậc chi tiết và các mức giảm trừ cụ thể (bản thân 11M, người phụ thuộc 4.4M/người, bảo hiểm 10.5%).
5. **Chỉ thị đầu ra (Output Indicator):** Quy trình 4 bước rất cụ thể từ phân tích, lập công thức, dry-run đến sinh code cuối cùng.

---

## 3. Phân tích lý do loại trừ các phương án còn lại

### ❌ Loại trừ Phương án A:
* **Nhược điểm:** Prompt quá thô sơ (Zero-shot) và mơ hồ.
* **Nguy cơ lỗi logic:** 
  * Cụm từ "theo biểu thuế Việt Nam hiện hành" rất nguy hiểm vì dữ liệu của AI có thể bị lỗi thời hoặc không cập nhật đúng luật thuế mới nhất.
  * Không chỉ định kiểu dữ liệu tài chính khiến AI nhiều khả năng sẽ dùng `double` hoặc `float`. Trong Java, phép tính số thực (Floating-point) luôn có sai số làm tròn (ví dụ: `0.1 + 0.2 = 0.30000000000000004`), gây chênh lệch tiền tệ và lỗi hệ thống tài chính Core HR.
  * Thiếu hoàn toàn các bước suy luận nên AI rất dễ viết sai ranh giới các bậc thuế.

### ❌ Loại trừ Phương án C:
* **Nhược điểm:** Sai lệch nghiêm trọng về tư duy tối ưu hóa (Over-engineering).
* **Nguy cơ lỗi logic:** 
  * Việc sử dụng "Java Stream API song song" (Parallel Stream) để tính toán 7 bậc thuế là hoàn toàn vô lý và phản tác dụng. Với 7 bậc thuế, số lượng phép tính là cực kỳ nhỏ (chưa tới 10 phép toán). Việc khởi tạo và quản lý ForkJoinPool cho Parallel Stream mất nhiều thời gian hơn hàng ngàn lần so với tính toán tuần tự thông thường, làm giảm hiệu năng hệ thống.
  * Stream song song có thể dẫn đến việc tính toán các bậc thuế không theo thứ tự tuần tự nếu không được lập trình cẩn thận, gây ra lỗi logic tính toán ranh giới thuế.
  * Không có ràng buộc về `BigDecimal` hay quy trình suy luận (CoT) để đảm bảo độ chính xác của số tiền thuế.

---

## 4. Phản hồi giả định của AI (Suy luận & Mã nguồn Java)

Dưới đây là nội dung phản hồi do AI sinh ra khi chạy Prompt tối ưu B:

### Bước 1: Phân tích cách xác định thu nhập tính thuế
Công thức xác định thu nhập tính thuế (TNTT):
$$\text{Thu nhập tính thuế} = \text{Tổng thu nhập} - \text{Các khoản giảm trừ}$$
Trong đó:
*   Giảm trừ gia cảnh bản thân: $11.000.000$ VND.
*   Giảm trừ người phụ thuộc: $\text{Số người phụ thuộc} \times 4.400.000$ VND.
*   Bảo hiểm bắt buộc: $\text{Tổng thu nhập} \times 10,5\%$.
*   *Lưu ý:* Nếu $\text{Thu nhập tính thuế} \le 0$, thuế TNCN phải nộp bằng 0.

### Bước 2: Công thức tính thuế cho từng bậc riêng lẻ
Thuế suất được tính lũy tiến từng phần theo các bậc sau:
*   **Bậc 1:** $\text{TNTT} \le 5M \rightarrow \text{Thuế} = \text{TNTT} \times 5\%$
*   **Bậc 2:** $5M < \text{TNTT} \le 10M \rightarrow \text{Thuế} = 250.000 + (\text{TNTT} - 5M) \times 10\%$
*   **Bậc 3:** $10M < \text{TNTT} \le 18M \rightarrow \text{Thuế} = 750.000 + (\text{TNTT} - 10M) \times 15\%$
*   **Bậc 4:** $18M < \text{TNTT} \le 32M \rightarrow \text{Thuế} = 1.950.000 + (\text{TNTT} - 18M) \times 20\%$
*   **Bậc 5:** $32M < \text{TNTT} \le 52M \rightarrow \text{Thuế} = 4.750.000 + (\text{TNTT} - 32M) \times 25\%$
*   **Bậc 6:** $52M < \text{TNTT} \le 80M \rightarrow \text{Thuế} = 9.750.000 + (\text{TNTT} - 52M) \times 30\%$
*   **Bậc 7:** $\text{TNTT} > 80M \rightarrow \text{Thuế} = 18.150.000 + (\text{TNTT} - 80M) \times 35\%$

### Bước 3: Dry-run chạy thử bằng tay
*   **Dữ liệu:** Tổng thu nhập = $30.000.000$ VND, 1 người phụ thuộc, không bảo hiểm.
*   **Tính giảm trừ:**
    *   Bản thân: $11.000.000$ VND.
    *   Người phụ thuộc: $1 \times 4.400.000 = 4.400.000$ VND.
    *   Bảo hiểm: $0$ VND.
    *   Tổng giảm trừ = $11.000.000 + 4.400.000 + 0 = 15.400.000$ VND.
*   **Thu nhập tính thuế:**
    *   $\text{TNTT} = 30.000.000 - 15.400.000 = 14.600.000$ VND.
*   **Tính thuế lũy tiến (TNTT = 14.6M rơi vào Bậc 3):**
    *   Bậc 1 (đến 5M): $5.000.000 \times 5\% = 250.000$ VND.
    *   Bậc 2 (từ 5M đến 10M): $(10.000.000 - 5.000.000) \times 10\% = 500.000$ VND.
    *   Bậc 3 (từ 10M đến 14.6M): $(14.600.000 - 10.000.000) \times 15\% = 4.600.000 \times 15\% = 690.000$ VND.
    *   **Tổng thuế TNCN** = $250.000 + 500.000 + 690.000 = 1.440.000$ VND.

---

### Bước 4: Mã nguồn Java hoàn chỉnh (Java 17 & BigDecimal)

```java
package com.corehr.payroll;

import java.math.BigDecimal;
import java.math.RoundingMode;

/**
 * Dịch vụ tính thuế thu nhập cá nhân lũy tiến sử dụng BigDecimal
 * Đảm bảo độ chính xác tuyệt đối cho hệ thống tài chính Core HR.
 */
public class TaxCalculator {

    // Các hằng số giảm trừ gia cảnh
    private static final BigDecimal PERSONAL_DEDUCTION = new BigDecimal("11000000");
    private static final BigDecimal DEPENDENT_DEDUCTION = new BigDecimal("4400000");
    private static final BigDecimal INSURANCE_RATE = new BigDecimal("0.105"); // 10.5%

    // Định nghĩa hạn mức của các bậc thuế (Bậc 1 đến Bậc 6, Bậc 7 là phần còn lại)
    private static final BigDecimal[] BRACKET_LIMITS = {
        new BigDecimal("5000000"),   // Bậc 1
        new BigDecimal("10000000"),  // Bậc 2
        new BigDecimal("18000000"),  // Bậc 3
        new BigDecimal("32000000"),  // Bậc 4
        new BigDecimal("52000000"),  // Bậc 5
        new BigDecimal("80000000")   // Bậc 6
    };

    // Định nghĩa thuế suất tương ứng của các bậc
    private static final BigDecimal[] TAX_RATES = {
        new BigDecimal("0.05"),  // Bậc 1: 5%
        new BigDecimal("0.10"),  // Bậc 2: 10%
        new BigDecimal("0.15"),  // Bậc 3: 15%
        new BigDecimal("0.20"),  // Bậc 4: 20%
        new BigDecimal("0.25"),  // Bậc 5: 25%
        new BigDecimal("0.30"),  // Bậc 6: 30%
        new BigDecimal("0.35")   // Bậc 7: 35%
    };

    /**
     * Hàm tính toán thuế TNCN lũy tiến từng phần
     * 
     * @param totalIncome Tổng thu nhập nhận được (VND)
     * @param dependents  Số lượng người phụ thuộc
     * @param hasInsurance Có tính bảo hiểm bắt buộc hay không
     * @return Thuế TNCN phải nộp (VND) tròn về 2 chữ số thập phân
     */
    public static BigDecimal calculatePIT(BigDecimal totalIncome, int dependents, boolean hasInsurance) {
        if (totalIncome == null || totalIncome.compareTo(BigDecimal.ZERO) <= 0) {
            return BigDecimal.ZERO.setScale(2, RoundingMode.HALF_UP);
        }

        // 1. Tính các khoản giảm trừ
        BigDecimal insuranceDeduction = BigDecimal.ZERO;
        if (hasInsurance) {
            insuranceDeduction = totalIncome.multiply(INSURANCE_RATE);
        }

        BigDecimal dependentDeductionTotal = DEPENDENT_DEDUCTION.multiply(BigDecimal.valueOf(dependents));
        BigDecimal totalDeductions = PERSONAL_DEDUCTION.add(dependentDeductionTotal).add(insuranceDeduction);

        // 2. Tính thu nhập tính thuế (TNTT)
        BigDecimal taxableIncome = totalIncome.subtract(totalDeductions);

        // Nếu TNTT <= 0 thì không phải nộp thuế
        if (taxableIncome.compareTo(BigDecimal.ZERO) <= 0) {
            return BigDecimal.ZERO.setScale(2, RoundingMode.HALF_UP);
        }

        // 3. Tính toán thuế lũy tiến từng phần
        BigDecimal totalTax = BigDecimal.ZERO;
        BigDecimal previousLimit = BigDecimal.ZERO;

        for (int i = 0; i < BRACKET_LIMITS.length; i++) {
            BigDecimal currentLimit = BRACKET_LIMITS[i];
            
            if (taxableIncome.compareTo(currentLimit) > 0) {
                // TNTT vượt qua bậc hiện tại, tính trọn vẹn bậc này
                BigDecimal bracketAmount = currentLimit.subtract(previousLimit);
                totalTax = totalTax.add(bracketAmount.multiply(TAX_RATES[i]));
                previousLimit = currentLimit;
            } else {
                // TNTT nằm trong bậc hiện tại, tính phần dư rồi kết thúc
                BigDecimal bracketAmount = taxableIncome.subtract(previousLimit);
                totalTax = totalTax.add(bracketAmount.multiply(TAX_RATES[i]));
                return totalTax.setScale(2, RoundingMode.HALF_UP);
            }
        }

        // Bậc 7: Phần thu nhập vượt trên 80 triệu
        BigDecimal bracketAmount = taxableIncome.subtract(previousLimit);
        totalTax = totalTax.add(bracketAmount.multiply(TAX_RATES[TAX_RATES.length - 1]));

        return totalTax.setScale(2, RoundingMode.HALF_UP);
    }

    // Hàm Main để chạy kiểm chứng Dry-run
    public static void main(String[] args) {
        BigDecimal totalIncome = new BigDecimal("30000000"); // 30 triệu VND
        int dependents = 1; // 1 người phụ thuộc
        boolean hasInsurance = false; // Không bảo hiểm

        BigDecimal tax = calculatePIT(totalIncome, dependents, hasInsurance);
        System.out.println("Tổng thu nhập: " + totalIncome + " VND");
        System.out.println("Thuế TNCN phải nộp: " + tax + " VND");
        // Kết quả mong đợi: 1440000.00 VND
    }
}
```
