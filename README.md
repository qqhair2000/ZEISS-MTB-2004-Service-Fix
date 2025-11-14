# 🛠️ ZEISS-MTB-2004-Service-Fix
Fix for ZEISS Axiovision 4.8.3 installation on Windows 11 where the MTB2004 service fails to install and start automatically. Includes guide for manual service registration using InstallUtil.exe.

# 🛠️ ZEISS MTB 2004 服務手動修復指南 (Windows 10/11)

**Repository 名稱建議:** `ZEISS-MTB-2004-Service-Fix`

**描述 (Description):**
> 解決 ZEISS Axiovision 4.8.3 / MTB 2004 在 Windows 10/11 等新系統上安裝後，服務無法自動註冊和啟動的問題。使用 InstallUtil.exe 手動註冊 MTBService 的完整指南。

---

## 🛑 問題描述 (The Problem)

在較新的 Windows 版本 (Windows 10, Windows 11) 上安裝舊版 **ZEISS Axiovision / MTB 2004** 軟體後，當使用者嘗試運行配置工具或執行特定功能 (例如導入配置) 時，會遇到以下錯誤：

1.  **IPC 連接錯誤：**
    ```
    連接到 IPC 端口失敗: 系統找不到指定的文件
    ```
2.  **程式碼執行錯誤：**
    ```
    ************** 例外狀況文字 **************
    System.NullReferenceException: 並未將物件參考設定為物件的執行個體
       於 ZEISS.MTB.Config.MainForm.importConfiguration_Click(Object sender, EventArgs e)
    ```

### 根本原因

`MTBConfig.exe` 需要一個後台服務 **`MTBService.exe`** 運行並提供 IPC 通信。然而，原有的安裝程式在新的 Windows 環境下未能成功地將 `MTBService.exe` 註冊為一個 Windows 服務。

---

## ✅ 解決方案：手動安裝 MTBService

要解決此問題，我們需要使用 **.NET Framework** 提供的官方工具 **`InstallUtil.exe`**，手動將服務程式註冊到 Windows 服務管理器中。

### 步驟 1：開啟「系統管理員」命令列工具

此操作需要寫入系統受保護的資料夾，因此必須使用管理員權限。

1.  在 Windows 搜尋欄中輸入 **CMD** (命令提示字元) 或 **PowerShell**。
2.  **右鍵點擊**搜尋結果，選擇 **“以系統管理員身分執行”**。

### 步驟 2：確認程式路徑

請找到兩個關鍵檔案的完整路徑：

| 檔案 | 典型路徑 (可能因安裝版本而異) |
| :--- | :--- |
| **`InstallUtil.exe`** | `C:\Windows\Microsoft.NET\Framework\v2.0.50727\InstallUtil.exe` |
| **`MTBService.exe`** | `C:\Program Files (x86)\Carl Zeiss\MTB 2004 - 1.8.1.8\MTB Server Console\MTBService.exe` |

### 步驟 3：執行服務安裝命令

在以系統管理員身份開啟的視窗中，執行以下命令。請根據您的實際路徑替換引號內的內容：

#### 🖥️ 推薦使用 PowerShell 命令

```powershell
& "C:\Windows\Microsoft.NET\Framework\v2.0.50727\InstallUtil.exe" "C:\Program Files (x86)\Carl Zeiss\MTB 2004 - 1.8.1.8\MTB Server Console\MTBService.exe"
```
成功提示： 執行成功後，您會看到數行輸出，最後顯示 “已經成功完成認可階段”。

### 步驟 4：啟動服務並設定為自動
服務已經安裝，但尚未運行。

開啟 “服務” 管理器：按下 Win + R 鍵，輸入 services.msc 並按 Enter。

在列表中找到名為 MTBService_1.8.1.8 (或類似名稱) 的服務。

右鍵點擊該服務，選擇 “啟動”。

雙擊該服務，將 “啟動類型” 設置為 “自動” (Automatic)，確保每次電腦重啟時服務都能自動運行。
完成以上步驟後，請重新運行您的 MTBConfig.exe，最初的 IPC 連接失敗和 NullReferenceException 錯誤將會被解決。
