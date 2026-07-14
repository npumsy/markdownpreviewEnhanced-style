# VSCode Markdown Preview Enhanced 插件配置指南

本文档介绍如何配置 VSCode 的 Markdown Preview Enhanced (MPE) 插件，包括 PrinceXML 的安装、中文乱码修复、字体配置以及样式自定义等。

---

## 1. 安装 PrinceXML (PDF 导出引擎)

PrinceXML 是 MPE 推荐的 PDF 导出引擎。以下是在 Ubuntu/WSL 环境下的安装步骤：

### 下载安装包
根据你的系统版本选择对应的下载链接：
- **Ubuntu 22.04**: [prince_16.1-1_amd64.deb](https://www.princexml.com/download/prince_16.1-1_ubuntu22.04_amd64.deb)
- **Ubuntu 20.04**: [prince_15.4.1-1_amd64.deb](https://www.princexml.com/download/prince_15.4.1-1_ubuntu20.04_amd64.deb)

```bash
wget https://www.princexml.com/download/prince_16.1-1_ubuntu22.04_amd64.deb
```

### 安装依赖工具
使用 `gdebi` 可以自动处理依赖关系：
```bash
sudo apt update
sudo apt install gdebi-core
```

### 执行安装
```bash
# 执行安装并安装依赖
sudo gdebi prince_16.1-1_ubuntu22.04_amd64.deb
```

### 验证安装
```bash
prince --version
```

---

## 2. 修复导出 PDF 中文乱码

在使用 MPE 导出 PDF 时，如果出现乱码（如 `�示�标�`），通常是由于缺少中文字体支持。

### 方法一：自定义 CSS
在 VSCode 中按 `Ctrl+Shift+P`，输入 `Markdown Preview Enhanced: Customize Css`。在打开的 `style.less` (或 `style.css`) 中添加：

```less
.markdown-preview.markdown-preview {
  font-family: 'Microsoft YaHei', sans-serif;
  
  /* Prince 导出设置 */
  &.prince {
    @page {
      @bottom {
        font-family: 'Microsoft YaHei', sans-serif;
        content: counter(page) " / " counter(pages);
      }
    }
  }
}
```

### 方法二：修改插件全局配置
1. 创建一个 `custom.css` 文件：
   ```css
   body {
     font-family: "Microsoft YaHei", "SimSun", "KaiTi", sans-serif;
   }
   ```
2. 在 VSCode 设置 (`settings.json`) 中引用该文件：
   ```json
   "markdown-preview-enhanced.previewTheme": "custom.css"
   ```

---

## 3. 在 WSL 中安装 Windows 字体

由于 WSL 可以直接访问 Windows 文件，我们可以直接将 Windows 下的字体复制到 Linux 环境中。

### 步骤 1：创建存放目录
```bash
sudo mkdir -p /usr/local/share/fonts/windows
```

### 步骤 2：拷贝常用字体文件
执行以下命令拷贝宋体、微软雅黑和 Times New Roman：

```bash
# 拷贝宋体 (SimSun)
sudo cp /mnt/c/Windows/Fonts/simsun.ttc /usr/local/share/fonts/windows/

# 拷贝微软雅黑 (Microsoft YaHei)
sudo cp /mnt/c/Windows/Fonts/msyh.ttc /usr/local/share/fonts/windows/
sudo cp /mnt/c/Windows/Fonts/msyhbd.ttc /usr/local/share/fonts/windows/
sudo cp /mnt/c/Windows/Fonts/msyhl.ttc /usr/local/share/fonts/windows/

# 拷贝 Times New Roman
sudo cp /mnt/c/Windows/Fonts/times.ttf /usr/local/share/fonts/windows/
sudo cp /mnt/c/Windows/Fonts/timesbd.ttf /usr/local/share/fonts/windows/
sudo cp /mnt/c/Windows/Fonts/timesbi.ttf /usr/local/share/fonts/windows/
sudo cp /mnt/c/Windows/Fonts/timesi.ttf /usr/local/share/fonts/windows/
```

### 步骤 3：刷新字体缓存 (关键)
```bash
# 修改权限
sudo chmod 644 /usr/local/share/fonts/windows/*

# 刷新缓存
sudo fc-cache -fv
```

### 步骤 4：验证安装
```bash
fc-list :lang=zh | grep "SimSun"
fc-list | grep "Times New Roman"
```

---

## 4. 样式自定义 (style.less)

你可以通过编辑 `style.less` 来调整预览和导出的排版。

### 设置字号与行高
```less
.markdown-preview {
  font-size: 16px;      /* 主体文字大小 */
  line-height: 1.6;     /* 行高 */
  color: #000000;       /* 纯黑色文字 */
  font-family: "Times New Roman", "SimSun", "Microsoft YaHei", serif;
}
```

### 限制图片宽度
为了防止大图片超出页面：
```less
.markdown-preview {
  img {
    max-width: 100% !important;
    height: auto !important;
    display: block;
    margin: 12px auto;
  }
}
```

### 详细排版优化
```less
.markdown-preview {
  // 标题样式
  h1 { font-size: 20px; margin-top: 24px; }
  h2 { font-size: 18px; margin-top: 20px; }
  h3 { font-size: 16px; margin-top: 16px; }

  // 表格样式
  table {
    font-size: 14px;
    border-collapse: collapse;
    width: 100%;
    margin: 12px 0;
    th, td {
      padding: 6px 10px;
      border: 1px solid #ddd;
    }
  }

  // 代码块样式
  pre {
    font-size: 12px;
    background-color: #f5f5f5;
    padding: 12px;
    border-radius: 6px;
    white-space: pre-wrap; /* 允许代码自动换行 */
    word-wrap: break-word;
  }
}
```
