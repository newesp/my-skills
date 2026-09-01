---
name: html-to-next
description: Convert native HTML files in temp/ into a maintainable, reusable Next.js component system and pages while preserving visual and layout fidelity.
---

請參考 `temp/` 目錄下的原生 HTML 文件，將其中的設計系統與頁面樣式完整還原到目前 Next.js 專案中，盡可能做到視覺、佈局、間距、顏色、字體、互動狀態一致。

### 具體要求：

1. **設計系統擷取**：先分析 `temp/` 中的 HTML/CSS 結構，擷取可重複使用的設計系統，包括：

 - 顏色、字體、間距、圓角、陰影等設計變數；
 - Button、Card、Input、Navigation、Layout 等通用組件；
 - 頁面層級佈局與響應式規則。
2. **前端架構建置**：在專案中建立清晰的前端架構：

 - 盡可能抽象可重複使用元件；
 - 避免在頁面中重複堆疊樣式。 
- 頁面應基於已有組件和設計規範開發
3. 編寫 `AGENTS.md` 文件，用於說明：

 - 專案整體介紹
 - 前端目錄結構
 - 設計系統架構
 - 元件復用規範
 - 後續開發注意事項
4. 在 `AGENTS.md` 中明確開發規格：

 - 開發頁面時，必須優先重複使用已有元件
 - 如果已有元件可以透過 props、variant、className 等方式擴展，應優先擴展，而不是重新建立相似元件
 - 只有在現有元件無法滿足需求時，才新增元件

5. **新增一個"設計系統預覽"頁面**：
 - 僅用於開發環境檢視
 - 採用純前端渲染
 - 展示顏色、字體、按鈕、卡片、表單、版面等核心元件
 - 用於驗證元件重複使用效果和樣式還原質量

最終目標：

1. 將 `temp/` 中的前端完整在目前 Next.js 中還原
2. 將 `temp/` 中的前端遷移為 Next.js 專案中的可維護、可重複使用、可擴充的前端元件體系