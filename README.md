# wallets-extension

# Polkadot Extension Demo 

This project shows how to use the Polkadot.js browser extension in a plain HTML page.

---

## 🔧 Setup Instructions

### 1. Initialize the project

```bash
npm init -y
```
### 2. Install the Polkadot extension package

```bash
npm install @polkadot/extension-dapp
```

### 3. Create expose.js

```jsx
import { web3Enable, web3Accounts } from '@polkadot/extension-dapp';

window.web3Enable = web3Enable;
window.web3Accounts = web3Accounts;
```
### 4. Install esbuild

```bash
npm install --save-dev esbuild
```

### 5. . Bundle it into web3-extension.js

```bash
npx esbuild expose.js --bundle --outfile=web3-extension.js --format=iife
```

### 6. Create test.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Polkadot.js Extension Example</title>
  <script src="./web3-extension.js"></script>
</head>
<body>
  <h1>Polkadot Extension Demo</h1>
  <button id="connect" disabled>Connect Wallet</button>
  <ul id="accounts"></ul>

  <script type="module">
    import {
      GearApi,
      ProgramMetadata
    } from 'https://esm.sh/@gear-js/api@0.37.0';

    const gearApi = await GearApi.create({
      providerAddress: 'wss://testnet.vara.network'
    });

    console.log('✅ Connected to:', await gearApi.chain());

   
  </script>

  <script>
    
    const waitForWeb3 = setInterval(() => {
      if (window.web3Enable && window.web3Accounts) {
        clearInterval(waitForWeb3);

        const btn = document.getElementById("connect");
        btn.disabled = false;

        btn.addEventListener("click", async () => {
          const extensions = await window.web3Enable("motoDEX");

          if (extensions.length === 0) {
            alert("No extension found or not enabled.");
            return;
          }

          const accounts = await window.web3Accounts();
          const list = document.getElementById("accounts");
          list.innerHTML = "";

          accounts.forEach((acc) => {
            const li = document.createElement("li");
            li.textContent = `${acc.meta.name || "Unknown"}: ${acc.address}`;
            list.appendChild(li);
          });
        });
      }
    }, 100);
  </script>
</body>
</html>

```

### 7. Run a local dev server

```bash
npx serve .
```
Open your browser at:

```bash
http://localhost:3000/test.html
```