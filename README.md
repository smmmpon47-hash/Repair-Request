<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>แจ้งซ่อมเครื่องจักร</title>
    <!-- Tailwind CSS (สำหรับจัดสไตล์หน้าเว็บ) -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- LIFF SDK -->
    <script charset="utf-8" src="https://static.line-scdn.net/liff/edge/2/sdk.js"></script>
</head>
<body class="bg-slate-100 min-h-screen p-4 flex justify-center items-center">

    <div class="bg-white rounded-2xl shadow-lg p-6 w-full max-w-md">
        <!-- หัวข้อฟอร์ม -->
        <h2 class="text-2xl font-bold text-emerald-600 text-center mb-6 flex items-center justify-center gap-2">
            🛠️ ฟอร์มแจ้งซ่อมเครื่องจักร
        </h2>

        <form id="repairForm" class="space-y-4">
            <!-- เลือกชั้น -->
            <div>
                <label class="block text-sm font-semibold text-gray-700 mb-1">ชั้น</label>
                <select id="floor" class="w-full p-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-emerald-500 focus:outline-none">
                    <option value="ชั้น 1">ชั้น 1</option>
                    <option value="ชั้น 2">ชั้น 2</option>
                    <option value="ชั้น 3" selected>ชั้น 3</option>
                </select>
            </div>

            <!-- สายการผลิต -->
            <div>
                <label class="block text-sm font-semibold text-gray-700 mb-1">LINE (สายการผลิต)</label>
                <select id="line" class="w-full p-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-emerald-500 focus:outline-none">
                    <option value="LINE A">LINE A</option>
                    <option value="LINE B" selected>LINE B</option>
                    <option value="LINE C">LINE C</option>
                </select>
            </div>

            <!-- ผู้แจ้ง (ดึงชื่อจาก LINE อัตโนมัติ) -->
            <div>
                <label class="block text-sm font-semibold text-gray-700 mb-1">ผู้แจ้ง</label>
                <input type="text" id="reporter" readonly placeholder="กำลังโหลดชื่อผู้แจ้ง..." class="w-full p-3 border border-gray-200 bg-gray-100 rounded-xl text-gray-600 outline-none cursor-not-allowed">
            </div>

            <!-- เครื่องจักร + ปุ่มสแกน QR -->
            <div>
                <label class="block text-sm font-semibold text-gray-700 mb-1">เครื่องจักร</label>
                <div class="flex gap-2">
                    <input type="text" id="machine" placeholder="พิมพ์ชื่อ/รหัสเครื่องจักร" class="w-full p-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-emerald-500 focus:outline-none" required>
                    <button type="button" onclick="scanQR()" class="bg-emerald-500 hover:bg-emerald-600 text-white font-medium px-4 py-3 rounded-xl flex items-center gap-1 transition-colors">
                        📷 QR
                    </button>
                </div>
            </div>

            <!-- อาการที่เสีย -->
            <div>
                <label class="block text-sm font-semibold text-gray-700 mb-1">อาการที่เสีย</label>
                <textarea id="issue" rows="3" placeholder="ระบุอาการเสีย หรือปัญหาที่พบ..." class="w-full p-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-emerald-500 focus:outline-none resize-none" required></textarea>
            </div>

            <!-- ปุ่มกดส่งข้อมูล -->
            <button type="submit" class="w-full bg-emerald-500 hover:bg-emerald-600 text-white font-bold py-3.5 rounded-xl transition-colors shadow-md mt-2">
                ส่งแจ้งซ่อม
            </button>
        </form>
    </div>

    <script>
        // ใส่ LIFF ID ที่ได้จาก LINE Developers Console ตรงนี้
        const MY_LIFF_ID = "วาง_LIFF_ID_ของคุ์ตรงนี้";

        // เริ่มต้นการทำงานของ LIFF
        async function main() {
            try {
                await liff.init({ liffId: MY_LIFF_ID });
                if (liff.isLoggedIn()) {
                    const profile = await liff.getProfile();
                    document.getElementById('reporter').value = profile.displayName;
                } else {
                    liff.login();
                }
            } catch (err) {
                console.error("LIFF Initialization failed", err);
            }
        }
        main();

        // ฟังก์ชันเปิดกล้องสแกน QR Code ผ่าน LIFF
        async function scanQR() {
            if (liff.scanCodeV2) {
                try {
                    const result = await liff.scanCodeV2();
                    if (result.value) {
                        document.getElementById('machine').value = result.value;
                    }
                } catch (err) {
                    console.error("Scan QR Error:", err);
                }
            } else {
                alert("อุปกรณ์ของคุณไม่รองรับการสแกน QR ผ่าน LIFF");
            }
        }

        // การทำงานเมื่อกดปุ่มส่งแจ้งซ่อม
        document.getElementById('repairForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            // ดึงค่าจากฟอร์มไปใช้งานต่อ (ส่งเข้า Apps Script หรือแสดงผล)
            alert("บันทึกข้อมูลสำเร็จ!");
            liff.closeWindow(); // ปิดหน้าต่าง LIFF
        });
    </script>
</body>
</html>
