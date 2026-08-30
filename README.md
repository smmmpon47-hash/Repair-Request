<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ระบบแจ้งซ่อมเครื่องจักร - LIFF</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Kanit:wght@300;400;500;600&display=swap" rel="stylesheet">
  <script src="https://static.line-scdn.net/liff/edge/2/sdk.js"></script>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Kanit', sans-serif; }
    body { background-color: #f0f2f5; color: #333; padding: 16px; display: flex; justify-content: center; }
    .form-container { background: #ffffff; width: 100%; max-width: 480px; border-radius: 16px; box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08); padding: 24px 20px; }
    .header-title { text-align: center; font-size: 22px; font-weight: 600; color: #1DB954; margin-bottom: 20px; padding-bottom: 12px; border-bottom: 2px solid #eef2f5; }
    .form-group { margin-bottom: 16px; }
    .form-label { display: block; font-size: 14px; font-weight: 500; color: #444; margin-bottom: 6px; }
    .form-control, select, textarea { width: 100%; padding: 12px 14px; font-size: 15px; border: 1px solid #dcdfe6; border-radius: 10px; background-color: #f8fafc; outline: none; transition: all 0.2s; }
    .form-control:focus, select:focus, textarea:focus { border-color: #1DB954; background-color: #fff; box-shadow: 0 0 0 3px rgba(29, 185, 84, 0.15); }
    .input-with-btn { display: flex; gap: 8px; }
    .input-with-btn input { flex: 1; }
    .btn-qr { background: #06C755; color: #ffffff; border: none; border-radius: 10px; padding: 0 14px; font-size: 14px; font-weight: 500; cursor: pointer; display: flex; align-items: center; gap: 4px; white-space: nowrap; box-shadow: 0 2px 6px rgba(6, 199, 85, 0.3); }
    .file-attach-btn { display: flex; align-items: center; justify-content: center; gap: 8px; width: 100%; padding: 12px; border: 2px dashed #cbd5e1; border-radius: 10px; background-color: #f8fafc; color: #64748b; font-size: 14px; font-weight: 500; cursor: pointer; }
    .image-preview { margin-top: 10px; display: none; width: 100%; max-height: 200px; border-radius: 10px; overflow: hidden; border: 1px solid #e2e8f0; }
    .image-preview img { width: 100%; height: 100%; object-fit: cover; }
    .button-group { display: flex; gap: 12px; margin-top: 24px; padding-top: 16px; border-top: 1px solid #f1f5f9; }
    .btn { flex: 1; padding: 13px; font-size: 16px; font-weight: 600; border-radius: 10px; border: none; cursor: pointer; text-align: center; }
    .btn-cancel { background-color: #f1f5f9; color: #64748b; }
    .btn-confirm { background-color: #1DB954; color: #ffffff; box-shadow: 0 4px 12px rgba(29, 185, 84, 0.3); }
    .btn-confirm:disabled { background-color: #a7f3d0; cursor: not-allowed; }
  </style>
</head>
<body>

  <div class="form-container">
    <div class="header-title">🛠️ ฟอร์มแจ้งซ่อมเครื่องจักร</div>

    <form id="repairForm">
      <!-- 1. ชั้น -->
      <div class="form-group">
        <label class="form-label" for="floorSelect">ชั้น</label>
        <select id="floorSelect" required onchange="updateLineOptions()">
          <option value="">-- กรุณาเลือกชั้น --</option>
          <option value="ชั้น 3">ชั้น 3</option>
          <option value="ชั้น 4">ชั้น 4</option>
        </select>
      </div>

      <!-- 2. LINE -->
      <div class="form-group">
        <label class="form-label" for="lineSelect">LINE (สายการผลิต)</label>
        <select id="lineSelect" required disabled>
          <option value="">-- กรุณาเลือกชั้นก่อน --</option>
        </select>
      </div>

      <!-- 3. ผู้แจ้ง -->
      <div class="form-group">
        <label class="form-label" for="reporterInput">ผู้แจ้ง</label>
        <input type="text" id="reporterInput" class="form-control" placeholder="กำลังโหลดชื่อผู้แจ้ง..." required>
      </div>

      <!-- 4. เครื่องจักร + ปุ่ม QR -->
      <div class="form-group">
        <label class="form-label" for="machineInput">เครื่องจักร</label>
        <div class="input-with-btn">
          <input type="text" id="machineInput" class="form-control" placeholder="พิมพ์ชื่อ/รหัสเครื่องจักร" required>
          <button type="button" class="btn-qr" onclick="scanMachineQR()">📷 QR</button>
        </div>
      </div>

      <!-- 5. อาการที่เสีย -->
      <div class="form-group">
        <label class="form-label" for="issueInput">อาการที่เสีย</label>
        <textarea id="issueInput" class="form-control" rows="3" placeholder="ระบุอาการเสีย หรือปัญหาที่พบ..." required></textarea>
      </div>

      <!-- 6. แนบรูป -->
      <div class="form-group">
        <label class="form-label">แนบรูปถ่าย</label>
        <div class="file-attach-wrapper">
          <input type="file" id="imageInput" style="display:none;" accept="image/*" capture="environment" onchange="previewImage(event)">
          <label for="imageInput" class="file-attach-btn" id="attachBtnText">📷 ถ่ายรูป / เลือกรูปภาพ</label>
          <div class="image-preview" id="imagePreview">
            <img id="previewImg" src="" alt="Preview">
          </div>
        </div>
      </div>

      <!-- ปุ่ม CANCEL และ CONFIRM -->
      <div class="button-group">
        <button type="button" class="btn btn-cancel" onclick="closeLiff()">CANCEL</button>
        <button type="submit" class="btn btn-confirm" id="submitBtn">CONFIRM</button>
      </div>
    </form>
  </div>

  <script>
    const GAS_WEB_APP_URL = "https://script.google.com/macros/s/AKfycbxUK2_MCCjW8TrCTjNHJj3AxvJQ2-w1tjHX5Cw6guNxallTCyvUKWqTB2dPtvzZtYbD/exec";
    const LIFF_ID = "2011319319-1bUfgtSJ";
    let liffUserId = "", base64Image = "";

    const lineData = {
      "ชั้น 3": ["B", "C"],
      "ชั้น 4": ["D", "E", "F", "G"]
    };

    async function main() {
      try {
        await liff.init({ liffId: LIFF_ID });
        if (liff.isLoggedIn()) {
          const profile = await liff.getProfile();
          liffUserId = profile.userId;
          document.getElementById('reporterInput').value = profile.displayName;
        } else {
          liff.login();
        }
      } catch (err) { console.error(err); }
    }
    main();

    function updateLineOptions() {
      const floorSelect = document.getElementById('floorSelect');
      const lineSelect = document.getElementById('lineSelect');
      const selectedFloor = floorSelect.value;
      lineSelect.innerHTML = '';

      if (selectedFloor && lineData[selectedFloor]) {
        lineSelect.disabled = false;
        lineSelect.appendChild(new Option('-- เลือก LINE --', ''));
        lineData[selectedFloor].forEach(line => {
          lineSelect.appendChild(new Option('LINE ' + line, 'LINE ' + line));
        });
      } else {
        lineSelect.disabled = true;
        lineSelect.appendChild(new Option('-- กรุณาเลือกชั้นก่อน --', ''));
      }
    }

    function scanMachineQR() {
      if (liff.scanCodeV2) {
        liff.scanCodeV2().then(result => {
          if (result.value) document.getElementById('machineInput').value = result.value;
        }).catch(err => alert("ไม่สามารถเปิดกล้องสแกนได้: " + err.message));
      } else {
        alert("อุปกรณ์ของคุณไม่รองรับการสแกน QR ผ่าน LIFF");
      }
    }

    // ฟังก์ชันบีบอัดขนาดรูปภาพก่อนส่ง ป้องกัน Data Crash
    function previewImage(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(e) {
          const img = new Image();
          img.onload = function() {
            const canvas = document.createElement('canvas');
            let width = img.width;
            let height = img.height;
            const max_size = 800;

            if (width > height) {
              if (width > max_size) {
                height *= max_size / width;
                width = max_size;
              }
            } else {
              if (height > max_size) {
                width *= max_size / height;
                height = max_size;
              }
            }
            canvas.width = width;
            canvas.height = height;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(img, 0, 0, width, height);

            base64Image = canvas.toDataURL('image/jpeg', 0.7);
            document.getElementById('previewImg').src = base64Image;
            document.getElementById('imagePreview').style.display = 'block';
            document.getElementById('attachBtnText').innerHTML = '🔄 เปลี่ยนรูปถ่าย';
          };
          img.src = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    }

    function closeLiff() {
      if (liff.isOpenForWindow()) liff.closeWindow();
    }

    document.getElementById('repairForm').addEventListener('submit', function(e) {
      e.preventDefault();
      const submitBtn = document.getElementById('submitBtn');
      submitBtn.disabled = true;
      submitBtn.innerText = "กำลังส่งข้อมูล...";

      const payload = {
        action: 'CREATE_REPAIR_JOB',
        floor: document.getElementById('floorSelect').value,
        line: document.getElementById('lineSelect').value,
        reporter: document.getElementById('reporterInput').value,
        machine: document.getElementById('machineInput').value,
        issue: document.getElementById('issueInput').value,
        image: base64Image,
        userId: liffUserId
      };

      const xhr = new XMLHttpRequest();
      xhr.open("POST", GAS_WEB_APP_URL, true);
      xhr.setRequestHeader("Content-Type", "text/plain;charset=utf-8");
      
      xhr.onreadystatechange = function() {
        if (xhr.readyState === 4) {
          alert("บันทึกข้อมูลแจ้งซ่อมเรียบร้อยแล้ว!");
          if (liff.isOpenForWindow()) {
            liff.closeWindow();
          }
        }
      };

      xhr.send(JSON.stringify(payload));
    });
  </script>
</body>
</html>
