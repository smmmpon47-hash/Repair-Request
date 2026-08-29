<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ระบบแจ้งซ่อมเครื่องจักร</title>
  <script src="https://static.line-scdn.net/liff/edge/2/sdk.js"></script>
  <style>
    body { font-family: 'Kanit', sans-serif; padding: 20px; background-color: #f4f6f9; }
    .card { background: white; padding: 20px; border-radius: 12px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
    h2 { text-align: center; color: #1DB954; }
    .form-group { margin-bottom: 15px; }
    label { display: block; margin-bottom: 5px; font-weight: bold; }
    select, input, textarea { width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px; box-sizing: border-box; }
    button { width: 100%; padding: 12px; background-color: #1DB954; color: white; border: none; border-radius: 6px; font-size: 16px; font-weight: bold; cursor: pointer; }
    button:disabled { background-color: #ccc; }
  </style>
</head>
<body>
  <div class="card">
    <h2>🛠️ แจ้งซ่อมเครื่องจักร</h2>
    <form id="repairForm">
      <div class="form-group">
        <label>เลือกโรงงาน:</label>
        <select id="factory" required>
          <option value="">-- กรุณาเลือกโรงงาน --</option>
          <option value="โรงงาน 1">โรงงาน 1</option>
          <option value="โรงงาน 2">โรงงาน 2</option>
        </select>
      </div>
      <div class="form-group">
        <label>ชื่อ/รหัสเครื่องจักร:</label>
        <input type="text" id="machine" placeholder="เช่น CNC-01" required>
      </div>
      <div class="form-group">
        <label>รายละเอียดอาการเสีย:</label>
        <textarea id="issue" rows="3" placeholder="ระบุอาการเสีย..." required></textarea>
      </div>
      <button type="submit" id="submitBtn">ส่งแจ้งซ่อม</button>
    </form>
  </div>

  <script>
    const GAS_URL = "วาง_WEB_APP_URL_ของ_GAS_ที่นี่";
    const LIFF_ID = "วาง_LIFF_ID_ที่นี่";
    let userId = "";
    let userName = "";

    async function main() {
      await liff.init({ liffId: LIFF_ID });
      if (liff.isLoggedIn()) {
        const profile = await liff.getProfile();
        userId = profile.userId;
        userName = profile.displayName;
      } else {
        liff.login();
      }
    }
    main();

    document.getElementById('repairForm').addEventListener('submit', async (e) => {
      e.preventDefault();
      document.getElementById('submitBtn').disabled = true;
      document.getElementById('submitBtn').innerText = "กำลังส่งข้อมูล...";

      const payload = {
        action: 'CREATE_REPAIR_JOB',
        factory: document.getElementById('factory').value,
        machine: document.getElementById('machine').value,
        issue: document.getElementById('issue').value,
        userId: userId,
        userName: userName
      };

      await fetch(GAS_URL, {
        method: 'POST',
        body: JSON.stringify(payload)
      });

      alert('แจ้งซ่อมเรียบร้อยแล้ว!');
      liff.closeWindow();
    });
  </script>
</body>
</html>
