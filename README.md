<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ClassCraft Studio - ระบบห้องเรียนพร้อมระบบลงทะเบียน</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Sarabun', sans-serif; }
        .glass {
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.5);
        }
    </style>
</head>
<body class="bg-gradient-to-br from-indigo-100 via-purple-50 to-pink-100 min-h-screen text-gray-800">

    <!-- Navbar -->
    <nav class="glass sticky top-0 z-50 px-6 py-4 flex justify-between items-center shadow-sm">
        <div class="flex items-center space-x-3">
            <div class="bg-indigo-600 text-white p-2 rounded-xl shadow-md font-bold text-lg">✨ CS</div>
            <span class="text-xl font-bold bg-gradient-to-r from-indigo-600 to-pink-600 bg-clip-text text-transparent">ClassCraft Studio</span>
        </div>
        
        <div class="flex items-center space-x-3">
            <div class="relative">
                <button onclick="openNotificationModal()" class="relative bg-white hover:bg-gray-100 p-2.5 rounded-xl border border-gray-200 shadow-sm transition text-gray-600" title="การแจ้งเตือน">
                    🔔
                    <span id="globalNotifBadge" class="absolute -top-1 -right-1 bg-red-500 text-white text-[10px] font-bold px-1.5 py-0.2 rounded-full hidden shadow">0</span>
                </button>
            </div>

            <div id="currentLoggedUser" class="hidden text-xs bg-pink-100 text-pink-700 px-3 py-1.5 rounded-xl font-bold border border-pink-200">
                👤 นร: <span id="userNameDisplay">-</span> (<span id="userIdDisplay">-</span>)
            </div>

            <button id="mainActionButton" onclick="handleMainAction()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-xl shadow-lg transition text-sm font-medium">
                + สร้างห้องเรียนใหม่
            </button>

            <button id="simulateStudentBtn" onclick="switchToStudentPrompt()" class="bg-pink-100 hover:bg-pink-200 text-pink-700 px-3 py-2 rounded-xl text-xs font-bold transition border border-pink-200">
                👁️ ไปมุมมองนักเรียน
            </button>

            <button id="openTeacherLoginBtn" onclick="openTeacherLoginModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-3 py-2 rounded-xl shadow transition text-xs font-medium hidden">
                👩‍🏫 เข้าสู่โหมดครู
            </button>

            <button id="setupTeacherPassBtn" onclick="openSetupPasswordModal()" class="bg-gray-200 hover:bg-gray-300 text-gray-700 px-2.5 py-2 rounded-xl text-xs font-semibold transition" title="ตั้งค่ารหัสผ่านโหมดครู">
                ⚙️
            </button>

            <div id="roleAvatar" class="w-10 h-10 rounded-full bg-indigo-200 flex items-center justify-center font-bold text-indigo-700 shadow-inner">คร</div>
        </div>
    </nav>

    <!-- Main Container -->
    <main class="max-w-6xl mx-auto p-6">
        <header class="mb-8">
            <h1 id="mainTitle" class="text-3xl font-bold text-gray-900">ห้องเรียนของฉัน (มุมมองคุณครูผู้ควบคุม)</h1>
            <p id="mainSubtitle" class="text-gray-500 mt-1">จัดการห้องเรียน สร้างงาน ประกาศข่าวสาร และตรวจสอบข้อมูลการลงทะเบียนของนักเรียน</p>
        </header>

        <!-- Classroom Grid -->
        <div id="classroom-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            <div class="glass rounded-2xl shadow-xl overflow-hidden flex flex-col justify-between class-item" data-code="MATH301" data-joined="true">
                <div class="bg-gradient-to-r from-blue-500 to-indigo-600 p-6 text-white relative">
                    <h3 class="text-xl font-bold">วิชาคณิตศาสตร์ ม.3</h3>
                    <p class="text-blue-100 text-sm mt-1">ครูสมชาย ใจดี</p>
                    <span class="absolute top-4 right-4 bg-white/20 text-xs px-2.5 py-1 rounded-full">รหัส: MATH301</span>
                </div>
                <div class="p-6 flex-1 flex flex-col justify-between">
                    <div>
                        <div class="flex justify-between text-sm text-gray-600 mb-4">
                            <span>นักเรียนลงทะเบียนแล้ว: <strong class="student-count text-indigo-600">2</strong> คน</span>
                            <span class="text-indigo-600 font-semibold">สถานะ: เปิดอยู่</span>
                        </div>
                    </div>
                    <div class="space-y-2">
                        <button onclick="openClassroom('วิชาคณิตศาสตร์ ม.3', 'MATH301')" class="w-full bg-indigo-50 hover:bg-indigo-100 text-indigo-600 border border-indigo-200 py-2 rounded-xl font-medium text-sm transition">
                            เข้าสู่ห้องเรียน
                        </button>
                        <button onclick="deleteClassroom('MATH301')" class="w-full teacher-only-feature bg-red-50 hover:bg-red-100 text-red-600 border border-red-200 py-1.5 rounded-xl font-medium text-xs transition">
                            🗑️ ลบห้องเรียนนี้
                        </button>
                        <button onclick="leaveClassroom('MATH301')" class="w-full student-only-feature bg-amber-50 hover:bg-amber-100 text-amber-700 border border-amber-200 py-1.5 rounded-xl font-medium text-xs transition hidden">
                            🚪 ถอนตัวออกจากห้องเรียน
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </main>

    <!-- Modal: หน้าภายในห้องเรียน -->
    <div id="roomModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-4xl shadow-2xl max-h-[90vh] flex flex-col">
            <div class="flex justify-between items-center border-b pb-4 mb-4">
                <div>
                    <h2 id="roomTitle" class="text-2xl font-bold text-gray-800">ชื่อห้องเรียน</h2>
                    <p id="roomCode" class="text-xs text-indigo-600 mt-0.5">รหัสห้องเรียน: -</p>
                </div>
                <button onclick="closeClassroom()" class="text-gray-400 hover:text-gray-600 font-bold text-xl">✕</button>
            </div>

            <!-- เมนูสลับแท็บ -->
            <div class="flex space-x-2 border-b border-gray-200 pb-3 mb-4">
                <button onclick="switchRoomTab('stream')" id="tabStreamBtn" class="relative px-4 py-2 rounded-xl font-bold text-sm bg-indigo-600 text-white shadow transition">
                    💬 กระดานประกาศ (Stream)
                    <span id="streamBadge" class="absolute -top-1.5 -right-1.5 bg-red-500 text-white text-[10px] font-bold px-1.5 py-0.5 rounded-full hidden shadow">0</span>
                </button>
                <button onclick="switchRoomTab('assignments')" id="tabAssignmentsBtn" class="relative px-4 py-2 rounded-xl font-bold text-sm bg-gray-100 text-gray-600 hover:bg-gray-200 transition">
                    📚 งานที่มอบหมาย (Assignments)
                    <span id="assignmentBadge" class="absolute -top-1.5 -right-1.5 bg-red-500 text-white text-[10px] font-bold px-1.5 py-0.5 rounded-full hidden shadow">0</span>
                </button>
            </div>

            <!-- ส่วนสำหรับครูควบคุม -->
            <div id="teacherControlBox" class="bg-indigo-50/70 p-4 rounded-2xl border border-indigo-100 mb-4 flex flex-col md:flex-row justify-between items-center gap-3">
                <div>
                    <h3 class="font-bold text-indigo-900 text-sm">👩‍🏫 แผงควบคุมคุณครู</h3>
                    <p class="text-xs text-indigo-700">ตรวจสอบทะเบียนรายชื่อนักเรียน หรือสร้างคำสั่งงานใหม่</p>
                </div>
                <div class="flex space-x-2">
                    <button onclick="openStudentListModal()" class="bg-purple-600 hover:bg-purple-700 text-white px-3.5 py-2 rounded-xl text-sm font-medium shadow transition">
                        👥 ทะเบียนนักเรียน (<span id="modalStudentCount">0</span>)
                    </button>
                    <button onclick="openAssignmentModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-3.5 py-2 rounded-xl text-sm font-medium shadow transition">
                        + สร้างคำสั่งงานใหม่
                    </button>
                </div>
            </div>

            <!-- CONTAINER 1: แท็บกระดานประกาศ (Stream) -->
            <div id="roomTabStream" class="overflow-y-auto flex-1 pr-2 space-y-4">
                <div class="bg-white p-4 rounded-2xl border border-indigo-100 shadow-sm">
                    <h4 class="font-bold text-xs text-indigo-900 mb-2" id="postBoxTitle">📢 ประกาศหรือพูดคุยอะไรกับชั้นเรียนของคุณ...</h4>
                    <textarea id="streamPostInput" rows="2" class="w-full p-3 text-sm border border-gray-200 rounded-xl focus:ring-2 focus:ring-indigo-500 outline-none mb-2" placeholder="เขียนข้อความหรือประกาศที่นี่..."></textarea>
                    <div class="flex justify-end">
                        <button onclick="postToStream()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-1.5 rounded-xl text-xs font-medium shadow transition">
                            โพสต์ข้อความ
                        </button>
                    </div>
                </div>
                <div id="streamPostsContainer" class="space-y-3"></div>
            </div>

            <!-- CONTAINER 2: แท็บรายการงาน (Assignments) -->
            <div id="roomTabAssignments" class="overflow-y-auto flex-1 pr-2 space-y-4 hidden">
                <div id="assignment-list" class="space-y-4">
                    <div class="bg-white p-5 rounded-2xl border border-gray-200 shadow-sm flex flex-col md:flex-row justify-between gap-4 assignment-card" data-id="assign-1" data-duedate="2026-09-30T23:59">
                        <div class="flex-1">
                            <span class="bg-indigo-100 text-indigo-700 text-xs px-2.5 py-1 rounded-full font-medium">การบ้าน</span>
                            <h3 class="font-bold text-gray-900 text-lg mt-2 assign-title-text">แบบฝึกหัดบทที่ 1: ระบบสุริยจักรวาล</h3>
                            <p class="text-sm text-gray-600 mt-1">ให้นักเรียนสรุปใจความสำคัญของดาวเคราะห์ในระบบสุริยะ</p>
                            <p class="text-xs text-red-500 font-semibold mt-3">🕒 กำหนดส่ง: 30 กันยายน 2569 เวลา 23:59 น.</p>
                            
                            <button onclick="openCheckSubmissionsModal('assign-1', 'แบบฝึกหัดบทที่ 1: ระบบสุริยจักรวาล')" class="mt-4 teacher-only-feature bg-amber-100 hover:bg-amber-200 text-amber-800 text-xs font-bold px-3 py-1.5 rounded-lg border border-amber-200 transition">
                                📊 ตรวจสถานะและให้คะแนน
                            </button>
                        </div>

                        <!-- มุมมองนักเรียนส่งงาน -->
                        <div class="w-full md:w-72 bg-gray-50 p-4 rounded-xl border border-gray-100 flex flex-col justify-between student-submit-box">
                            <div>
                                <h4 class="font-bold text-xs text-gray-700 mb-2">📤 ส่งงานของคุณ</h4>
                                <div id="uploadBox-assign-1">
                                    <input type="file" id="file-assign-1" accept="image/*,.pdf,.doc,.docx" class="text-xs text-gray-500 file:mr-2 file:py-1.5 file:px-3 file:rounded-lg file:border-0 file:text-xs file:font-semibold file:bg-indigo-50 file:text-indigo-700 mb-2 w-full">
                                </div>
                                <div id="status-assign-1" class="text-xs font-semibold text-gray-500 mb-2">สถานะ: ยังไม่ได้ส่ง</div>
                                <div id="feedback-assign-1" class="text-xs bg-indigo-50 p-2 rounded-lg text-indigo-900 hidden mb-2 border border-indigo-100"></div>
                            </div>
                            <div id="actionBtnBox-assign-1">
                                <button onclick="submitAssignment('assign-1')" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white py-1.5 rounded-lg text-xs font-medium shadow">
                                    ส่งงาน (Turn In)
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal: ลงทะเบียนเข้าห้องเรียน (Registration Modal) -->
    <div id="joinModal" class="fixed inset-0 bg-black/50 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-md shadow-2xl border-2 border-pink-100">
            <div class="text-center mb-4">
                <span class="bg-pink-100 text-pink-700 text-xs font-bold px-3 py-1 rounded-full">📝 ระบบลงทะเบียนนักเรียน</span>
                <h2 class="text-xl font-bold mt-2 text-gray-800">กรอกข้อมูลเพื่อลงทะเบียนเข้าห้องเรียน</h2>
                <p class="text-xs text-gray-500 mt-1">นักเรียนจะต้องลงทะเบียนและระบุรหัสประจำตัวก่อนเข้าห้องเรียนทุกครั้ง</p>
            </div>
            <form onsubmit="registerAndJoinClassroom(event)">
                <label class="block text-xs font-bold text-gray-700 mb-1">รหัสประจำตัวนักเรียน (Student ID)</label>
                <input type="text" id="studentIdInput" required class="w-full px-4 py-2 border rounded-xl mb-3 focus:ring-2 focus:ring-pink-500 outline-none text-sm font-semibold" placeholder="เช่น 68102">
                
                <label class="block text-xs font-bold text-gray-700 mb-1">ชื่อ - นามสกุล นักเรียน</label>
                <input type="text" id="studentNameInput" required class="w-full px-4 py-2 border rounded-xl mb-3 focus:ring-2 focus:ring-pink-500 outline-none text-sm" placeholder="เช่น เด็กชายรักเรียน เพียรศึกษา">
                
                <label class="block text-xs font-bold text-gray-700 mb-1">รหัสห้องเรียน (Class Code)</label>
                <input type="text" id="joinCodeInput" required class="w-full px-4 py-2 border rounded-xl mb-6 focus:ring-2 focus:ring-pink-500 outline-none uppercase tracking-wider font-bold text-center text-lg text-pink-600" placeholder="เช่น MATH301">
                
                <div class="flex justify-end space-x-3">
                    <button type="button" onclick="closeJoinModal()" class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded-xl text-sm">ยกเลิก</button>
                    <button type="submit" class="px-4 py-2 bg-pink-600 hover:bg-pink-700 text-white rounded-xl text-sm font-medium shadow-md">ลงทะเบียนและเข้าห้องเรียน</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal: ตรวจงาน ให้คะแนน และคอมเมนต์ส่วนตัว (สำหรับครู) -->
    <div id="checkSubmissionsModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-xl shadow-2xl">
            <h2 class="text-xl font-bold mb-1 text-gray-800">📊 ตรวจงานและให้คะแนนนักเรียน</h2>
            <p id="checkAssignTitle" class="text-xs text-indigo-600 font-semibold mb-4">หัวข้องาน: -</p>
            
            <div class="flex justify-between items-center mb-2 px-1 text-xs text-gray-500 font-bold">
                <span>รายชื่อนักเรียนที่ลงทะเบียน</span>
                <span>สถานะและตรวจงาน</span>
            </div>

            <div id="submissionsListContainer" class="bg-gray-50 border border-gray-200 rounded-2xl p-3 max-h-72 overflow-y-auto space-y-2 mb-4"></div>

            <div class="flex justify-end">
                <button type="button" onclick="closeCheckSubmissionsModal()" class="px-4 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 rounded-xl text-sm font-medium">ปิด</button>
            </div>
        </div>
    </div>

    <!-- Modal: ให้คะแนนและคอมเมนต์รายบุคคล -->
    <div id="gradeStudentModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-lg shadow-2xl">
            <h2 class="text-xl font-bold mb-1 text-gray-800">✍️ ให้คะแนนและข้อเสนอแนะ</h2>
            <p id="gradeModalStudentName" class="text-xs text-indigo-600 font-semibold mb-3">นักเรียน: -</p>
            
            <div class="bg-indigo-50/60 p-4 rounded-2xl border border-indigo-100 mb-4 space-y-3">
                <div class="flex justify-between items-center text-xs">
                    <span>📁 ไฟล์งาน: <strong id="gradeModalFileName" class="text-indigo-900">-</strong></span>
                    <button onclick="openPreviewWork()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-2.5 py-1 rounded-lg font-medium shadow">🔍 เปิดพรีวิวไฟล์</button>
                </div>
                <div>
                    <span class="text-xs font-bold text-gray-500 block mb-1">🕒 เวลาส่ง: <span id="gradeModalTime" class="font-normal text-gray-700">-</span></span>
                </div>

                <hr class="border-indigo-100">

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-gray-700 mb-1">ให้คะแนน (เช่น 10/10)</label>
                        <input type="text" id="inputGradeScore" class="w-full px-3 py-2 text-sm bg-white border rounded-xl outline-none font-bold text-indigo-600" placeholder="เช่น 9.5">
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-bold text-gray-700 mb-1">💬 คอมเมนต์ส่วนตัวถึงนักเรียน</label>
                    <textarea id="inputPrivateComment" rows="2" class="w-full p-2.5 text-sm bg-white border rounded-xl outline-none" placeholder="พิมพ์ข้อความแนะนำตินชมงานนี้..."></textarea>
                </div>
            </div>

            <div class="flex justify-end space-x-2">
                <button type="button" onclick="saveGradeAndFeedback()" class="px-4 py-2 bg-green-600 hover:bg-green-700 text-white rounded-xl text-sm font-medium shadow">💾 บันทึกคะแนนและส่งคอมเมนต์</button>
                <button type="button" onclick="closeGradeStudentModal()" class="px-4 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 rounded-xl text-sm font-medium">ปิด</button>
            </div>
        </div>
    </div>

    <!-- Modal: พรีวิวไฟล์งานออนไลน์ -->
    <div id="filePreviewModal" class="fixed inset-0 bg-black/60 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-2xl shadow-2xl flex flex-col max-h-[85vh]">
            <div class="flex justify-between items-center border-b pb-3 mb-3">
                <h3 class="font-bold text-gray-800 text-base">🔍 พรีวิวไฟล์งาน: <span id="previewFileNameTitle" class="text-indigo-600">-</span></h3>
                <button onclick="closeFilePreviewModal()" class="text-gray-400 hover:text-gray-600 font-bold text-lg">✕</button>
            </div>
            <div id="previewContentContainer" class="flex-1 bg-gray-100 rounded-2xl flex items-center justify-center overflow-auto p-4 border border-gray-200 min-h-[300px]">
                <p class="text-sm text-gray-500">กำลังโหลดตัวอย่างไฟล์...</p>
            </div>
            <div class="flex justify-end mt-4">
                <button onclick="closeFilePreviewModal()" class="px-4 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 rounded-xl text-sm font-medium">ปิดหน้าต่าง</button>
            </div>
        </div>
    </div>

    <!-- Modal: ศูนย์รวมการแจ้งเตือน -->
    <div id="notificationModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-md shadow-2xl">
            <div class="flex justify-between items-center border-b pb-3 mb-3">
                <h2 class="text-lg font-bold text-gray-800">🔔 ประวัติการแจ้งเตือนทั้งหมด</h2>
                <button onclick="closeNotificationModal()" class="text-gray-400 hover:text-gray-600 font-bold">✕</button>
            </div>
            <div id="notificationListContainer" class="space-y-2 max-h-72 overflow-y-auto pr-1">
                <p class="text-xs text-gray-400 text-center py-6">ยังไม่มีการแจ้งเตือนใหม่ในขณะนี้</p>
            </div>
            <div class="flex justify-end mt-4">
                <button onclick="closeNotificationModal()" class="px-4 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 rounded-xl text-sm font-medium">ปิด</button>
            </div>
        </div>
    </div>

    <!-- Modal: ดูทะเบียนรายชื่อนักเรียน (สำหรับครู) -->
    <div id="studentListModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-md shadow-2xl">
            <h2 class="text-xl font-bold mb-1 text-gray-800">👥 ทะเบียนรายชื่อนักเรียน</h2>
            <p class="text-xs text-gray-500 mb-4">รายชื่อและรหัสประจำตัวเด็กๆ ที่ลงทะเบียนเข้าเรียนในห้องนี้</p>
            <div id="studentListContainer" class="bg-gray-50 border border-gray-200 rounded-2xl p-3 max-h-60 overflow-y-auto space-y-2 mb-4"></div>
            <div class="flex justify-end">
                <button type="button" onclick="closeStudentListModal()" class="px-4 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 rounded-xl text-sm font-medium">ปิด</button>
            </div>
        </div>
    </div>

    <!-- Modal: สร้างห้องเรียนใหม่ -->
    <div id="createModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-md shadow-2xl">
            <h2 class="text-xl font-bold mb-4 text-gray-800">สร้างห้องเรียนใหม่</h2>
            <form onsubmit="addClassroom(event)">
                <label class="block text-sm font-medium text-gray-700 mb-1">ชื่อวิชา / ห้องเรียน</label>
                <input type="text" id="className" required class="w-full px-4 py-2 border rounded-xl mb-4 focus:ring-2 focus:ring-indigo-500 outline-none" placeholder="เช่น วิชาวิทยาศาสตร์ ม.1">
                <label class="block text-sm font-medium text-gray-700 mb-1">ชื่อคุณครูผู้สอน</label>
                <input type="text" id="teacherName" required class="w-full px-4 py-2 border rounded-xl mb-6 focus:ring-2 focus:ring-indigo-500 outline-none" placeholder="เช่น ครูดารารัตน์">
                <div class="flex justify-end space-x-3">
                    <button type="button" onclick="closeCreateModal()" class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded-xl text-sm">ยกเลิก</button>
                    <button type="submit" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-xl text-sm font-medium shadow-md">สร้างห้องเรียน</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal: จำลองมุมมองนักเรียน -->
    <div id="studentPromptModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-sm shadow-2xl">
            <h2 class="text-xl font-bold mb-2 text-gray-800">👁️ สลับมุมมองนักเรียน</h2>
            <p class="text-xs text-gray-500 mb-4">กรอกข้อมูลนักเรียนเพื่อจำลองการใช้งาน</p>
            <form onsubmit="confirmStudentSimulation(event)">
                <label class="block text-sm font-medium text-gray-700 mb-1">รหัสประจำตัวนักเรียน</label>
                <input type="text" id="simulateIdInput" required class="w-full px-4 py-2 border rounded-xl mb-3 focus:ring-2 focus:ring-pink-500 outline-none text-sm" placeholder="เช่น 68101">
                <label class="block text-sm font-medium text-gray-700 mb-1">ชื่อนักเรียน</label>
                <input type="text" id="simulateNameInput" required class="w-full px-4 py-2 border rounded-xl mb-6 focus:ring-2 focus:ring-pink-500 outline-none text-sm" placeholder="เช่น ด.ช. อภิสิทธิ์ เรียนดี">
                <div class="flex justify-end space-x-3">
                    <button type="button" onclick="closeStudentPromptModal()" class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded-xl text-sm">ยกเลิก</button>
                    <button type="submit" class="px-4 py-2 bg-pink-600 hover:bg-pink-700 text-white rounded-xl text-sm font-medium shadow-md">เข้าสู่โหมดนักเรียน</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal: ใส่รหัสผ่านเข้าโหมดครู -->
    <div id="teacherLoginModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-sm shadow-2xl">
            <h2 class="text-xl font-bold mb-2 text-gray-800">🔐 เข้าสู่โหมดคุณครู</h2>
            <p class="text-xs text-gray-500 mb-4">กรอกรหัสผ่านเพื่อควบคุมระบบ</p>
            <form onsubmit="verifyTeacherPassword(event)">
                <label class="block text-sm font-medium text-gray-700 mb-1">รหัสผ่านโหมดครู</label>
                <input type="password" id="teacherPassInput" required class="w-full px-4 py-2 border rounded-xl mb-6 focus:ring-2 focus:ring-indigo-500 outline-none text-center font-bold tracking-widest text-lg" placeholder="••••••••">
                <div class="flex justify-end space-x-3">
                    <button type="button" onclick="closeTeacherLoginModal()" class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded-xl text-sm">ยกเลิก</button>
                    <button type="submit" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-xl text-sm font-medium shadow-md">ยืนยัน</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal: ตั้งรหัสผ่านโหมดครู -->
    <div id="setupPasswordModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-sm shadow-2xl">
            <h2 class="text-xl font-bold mb-2 text-gray-800">⚙️ กำหนดรหัสผ่านโหมดครู</h2>
            <form onsubmit="saveTeacherPassword(event)">
                <label class="block text-sm font-medium text-gray-700 mb-1">รหัสผ่านใหม่</label>
                <input type="password" id="newTeacherPass" required class="w-full px-4 py-2 border rounded-xl mb-4 focus:ring-2 focus:ring-indigo-500 outline-none" placeholder="รหัสผ่านใหม่">
                <div class="flex justify-end space-x-3">
                    <button type="button" onclick="closeSetupPasswordModal()" class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded-xl text-sm">ยกเลิก</button>
                    <button type="submit" class="px-4 py-2 bg-green-600 hover:bg-green-700 text-white rounded-xl text-sm font-medium shadow-md">บันทึก</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal: สร้างคำสั่งงานใหม่ -->
    <div id="assignmentModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="glass bg-white rounded-3xl p-6 w-full max-w-md shadow-2xl">
            <h2 class="text-xl font-bold mb-4 text-gray-800">📝 สร้างคำสั่งงานใหม่</h2>
            <form onsubmit="addAssignment(event)">
                <label class="block text-sm font-medium text-gray-700 mb-1">หัวข้องาน</label>
                <input type="text" id="assignTitle" required class="w-full px-4 py-2 border rounded-xl mb-3 focus:ring-2 focus:ring-indigo-500 outline-none" placeholder="เช่น รายงานเศรษฐศาสตร์เบื้องต้น">
                <label class="block text-sm font-medium text-gray-700 mb-1">รายละเอียด / คำชี้แจง</label>
                <textarea id="assignDesc" rows="3" required class="w-full px-4 py-2 border rounded-xl mb-3 focus:ring-2 focus:ring-indigo-500 outline-none" placeholder="ระบุรายละเอียด..."></textarea>
                <div class="grid grid-cols-2 gap-3 mb-6">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">กำหนดส่ง (วันที่)</label>
                        <input type="date" id="assignDate" required class="w-full px-3 py-2 border rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">เวลา</label>
                        <input type="time" id="assignTime" value="23:59" required class="w-full px-3 py-2 border rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                </div>
                <div class="flex justify-end space-x-3">
                    <button type="button" onclick="closeAssignmentModal()" class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded-xl text-sm">ยกเลิก</button>
                    <button type="submit" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-xl text-sm font-medium shadow-md">มอบหมายงาน</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Script ระบบจัดการข้อมูลห้องเรียนและทะเบียนนักเรียน -->
    <script>
        let currentRole = 'teacher'; 
        let teacherPassword = localStorage.getItem('teacherPass') || 'teacher123';
        let currentStudentName = ''; 
        let currentStudentId = '';
        
        let classStudents = {
            'MATH301': [
                { id: '68001', name: 'ด.ช. อภิสิทธิ์ เรียนดี' },
                { id: '68002', name: 'ด.ญ. สมหญิง รักเรียน' }
            ]
        };

        let submissions = {
            'MATH301': {
                'assign-1': {
                    'ด.ช. อภิสิทธิ์ เรียนดี': { 
                        status: 'submitted', 
                        time: '17 ก.ย. 2569 เวลา 10:00 น.', 
                        timestamp: new Date('2026-09-17T10:00:00').getTime(),
                        fileName: 'homework_report.pdf', 
                        fileData: null,
                        isLate: false,
                        grade: '9.5 / 10', 
                        comment: 'ทำสรุปเนื้อหาออกมาได้เข้าใจง่ายดีมากครับ' 
                    }
                }
            }
        };

        let streamPosts = {
            'MATH301': [
                {
                    author: '👩‍🏫 ครูสมชาย ใจดี',
                    role: 'teacher',
                    time: '17 ก.ย. 2569 เวลา 08:30 น.',
                    text: 'ยินดีต้อนรับนักเรียนทุกคนเข้าสู่ห้องเรียนวิชาคณิตศาสตร์ ม.3 ครับ อย่าลืมลงทะเบียนก่อนเข้าเรียนนะครับ'
                }
            ]
        };

        let unreadCounts = {
            'MATH301': { stream: 0, assignments: 0 }
        };

        let globalNotifications = []; 
        let activeRoomCode = ''; 
        let activeCheckAssignId = '';
        let gradingStudentName = '';

        function switchRole(role, studentName = '', studentId = '') {
            currentRole = role;
            const mainActionButton = document.getElementById('mainActionButton');
            const teacherControlBox = document.getElementById('teacherControlBox');
            const mainTitle = document.getElementById('mainTitle');
            const mainSubtitle = document.getElementById('mainSubtitle');
            const roleAvatar = document.getElementById('roleAvatar');
            const simulateStudentBtn = document.getElementById('simulateStudentBtn');
            const openTeacherLoginBtn = document.getElementById('openTeacherLoginBtn');
            const setupTeacherPassBtn = document.getElementById('setupTeacherPassBtn');
            const currentLoggedUser = document.getElementById('currentLoggedUser');
            const userNameDisplay = document.getElementById('userNameDisplay');
            const userIdDisplay = document.getElementById('userIdDisplay');

            if (role === 'teacher') {
                simulateStudentBtn.style.display = 'block';
                openTeacherLoginBtn.style.display = 'block';
                setupTeacherPassBtn.style.display = 'block';
                currentLoggedUser.classList.add('hidden');

                mainActionButton.innerText = "+ สร้างห้องเรียนใหม่";
                mainActionButton.className = "bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-xl shadow-lg transition text-sm font-medium";
                teacherControlBox.classList.remove('hidden');
                mainTitle.innerText = "ห้องเรียนของฉัน (มุมมองคุณครูผู้ควบคุม)";
                mainSubtitle.innerText = "จัดการห้องเรียน สร้างงาน ประกาศข่าวสาร และตรวจสอบข้อมูลการลงทะเบียนของนักเรียน";
                roleAvatar.innerText = "คร";
                roleAvatar.className = "w-10 h-10 rounded-full bg-indigo-200 flex items-center justify-center font-bold text-indigo-700 shadow-inner";
                
                toggleTeacherStudentUI(true);
            } else {
                currentStudentName = studentName;
                currentStudentId = studentId;
                simulateStudentBtn.style.display = 'block';
                openTeacherLoginBtn.style.display = 'block';
                setupTeacherPassBtn.style.display = 'none';
                currentLoggedUser.classList.remove('hidden');
                userNameDisplay.innerText = studentName;
                userIdDisplay.innerText = studentId;

                mainActionButton.innerText = "+ ลงทะเบียนเข้าห้องเรียนอื่น";
                mainActionButton.className = "bg-pink-600 hover:bg-pink-700 text-white px-4 py-2 rounded-xl shadow-lg transition text-sm font-medium";
                teacherControlBox.classList.add('hidden');
                mainTitle.innerText = "ห้องเรียนของฉัน (มุมมองนักเรียน)";
                mainSubtitle.innerText = `ยินดีต้อนรับ คุณ ${studentName} (รหัส: ${studentId}) - ส่งงาน ตรวจเช็กคะแนนและคอมเมนต์ส่วนตัวได้ที่นี่`;
                roleAvatar.innerText = "นร";
                roleAvatar.className = "w-10 h-10 rounded-full bg-pink-200 flex items-center justify-center font-bold text-pink-700 shadow-inner";
                
                toggleTeacherStudentUI(false);
            }
            updateClassroomVisibility();
        }

        function toggleTeacherStudentUI(isTeacher) {
            const teacherFeatures = document.querySelectorAll('.teacher-only-feature');
            const studentFeatures = document.querySelectorAll('.student-only-feature');
            const studentBoxes = document.querySelectorAll('.student-submit-box');
            const postBoxTitle = document.getElementById('postBoxTitle');

            teacherFeatures.forEach(el => el.style.display = isTeacher ? 'inline-block' : 'none');
            studentFeatures.forEach(el => el.style.display = isTeacher ? 'none' : 'block');
            studentBoxes.forEach(el => el.style.display = isTeacher ? 'none' : 'flex');
            
            if (postBoxTitle) {
                postBoxTitle.innerText = isTeacher ? "📢 โพสต์ประกาศข่าวสารสำคัญถึงชั้นเรียน..." : "💬 โพสต์ข้อความหรือถามคำถามในชั้นเรียน...";
            }
        }

        function switchRoomTab(tabName) {
            const tabStreamBtn = document.getElementById('tabStreamBtn');
            const tabAssignmentsBtn = document.getElementById('tabAssignmentsBtn');
            const roomTabStream = document.getElementById('roomTabStream');
            const roomTabAssignments = document.getElementById('roomTabAssignments');

            if (!unreadCounts[activeRoomCode]) unreadCounts[activeRoomCode] = { stream: 0, assignments: 0 };

            if (tabName === 'stream') {
                tabStreamBtn.className = "relative px-4 py-2 rounded-xl font-bold text-sm bg-indigo-600 text-white shadow transition";
                tabAssignmentsBtn.className = "relative px-4 py-2 rounded-xl font-bold text-sm bg-gray-100 text-gray-600 hover:bg-gray-200 transition";
                roomTabStream.classList.remove('hidden');
                roomTabAssignments.classList.add('hidden');
                unreadCounts[activeRoomCode].stream = 0;
            } else {
                tabAssignmentsBtn.className = "relative px-4 py-2 rounded-xl font-bold text-sm bg-indigo-600 text-white shadow transition";
                tabStreamBtn.className = "relative px-4 py-2 rounded-xl font-bold text-sm bg-gray-100 text-gray-600 hover:bg-gray-200 transition";
                roomTabAssignments.classList.remove('hidden');
                roomTabStream.classList.add('hidden');
                unreadCounts[activeRoomCode].assignments = 0;
            }
            updateNotificationBadges();
        }

        function updateNotificationBadges() {
            if (!activeRoomCode || !unreadCounts[activeRoomCode]) return;
            const counts = unreadCounts[activeRoomCode];
            const streamBadge = document.getElementById('streamBadge');
            const assignmentBadge = document.getElementById('assignmentBadge');

            if (counts.stream > 0) {
                streamBadge.innerText = counts.stream;
                streamBadge.classList.remove('hidden');
            } else { streamBadge.classList.add('hidden'); }

            if (counts.assignments > 0) {
                assignmentBadge.innerText = counts.assignments;
                assignmentBadge.classList.remove('hidden');
            } else { assignmentBadge.classList.add('hidden'); }
        }

        function addGlobalNotification(message) {
            globalNotifications.unshift({
                text: message,
                time: new Date().toLocaleTimeString('th-TH', { hour: '2-digit', minute: '2-digit' })
            });
            const badge = document.getElementById('globalNotifBadge');
            badge.innerText = globalNotifications.length;
            badge.classList.remove('hidden');
            renderNotificationList();
        }

        function openNotificationModal() {
            renderNotificationList();
            document.getElementById('notificationModal').classList.remove('hidden');
        }
        function closeNotificationModal() { document.getElementById('notificationModal').classList.add('hidden'); }

        function renderNotificationList() {
            const container = document.getElementById('notificationListContainer');
            container.innerHTML = '';
            if (globalNotifications.length === 0) {
                container.innerHTML = '<p class="text-xs text-gray-400 text-center py-6">ยังไม่มีการแจ้งเตือนใหม่ในขณะนี้</p>';
                return;
            }
            globalNotifications.forEach(notif => {
                const item = document.createElement('div');
                item.className = "bg-white p-3 rounded-xl border border-gray-100 shadow-sm text-sm flex justify-between items-center";
                item.innerHTML = `<span class="text-gray-800">${notif.text}</span> <span class="text-[10px] text-gray-400">${notif.time}</span>`;
                container.appendChild(item);
            });
        }

        function switchToStudentPrompt() { document.getElementById('studentPromptModal').classList.remove('hidden'); }
        function closeStudentPromptModal() { document.getElementById('studentPromptModal').classList.add('hidden'); }
        function confirmStudentSimulation(event) {
            event.preventDefault();
            const id = document.getElementById('simulateIdInput').value.trim();
            const name = document.getElementById('simulateNameInput').value.trim();
            closeStudentPromptModal();
            switchRole('student', name, id);
            alert(`👁️ สลับมุมมองเป็นนักเรียน: "${name}" เรียบร้อยแล้ว`);
        }

        function openTeacherLoginModal() { document.getElementById('teacherLoginModal').classList.remove('hidden'); }
        function closeTeacherLoginModal() { document.getElementById('teacherLoginModal').classList.add('hidden'); document.getElementById('teacherPassInput').value = ''; }
        function verifyTeacherPassword(event) {
            event.preventDefault();
            if (document.getElementById('teacherPassInput').value === teacherPassword) {
                closeTeacherLoginModal();
                switchRole('teacher');
                alert('🔓 ยืนยันตัวตนสำเร็จ! เข้าสู่โหมดคุณครูผู้ควบคุมแล้ว');
            } else { alert('❌ รหัสผ่านไม่ถูกต้อง!'); }
        }

        function openSetupPasswordModal() { document.getElementById('setupPasswordModal').classList.remove('hidden'); }
        function closeSetupPasswordModal() { document.getElementById('setupPasswordModal').classList.add('hidden'); document.getElementById('newTeacherPass').value = ''; }
        function saveTeacherPassword(event) {
            event.preventDefault();
            const newPass = document.getElementById('newTeacherPass').value;
            if (newPass.trim() !== '') {
                teacherPassword = newPass;
                localStorage.setItem('teacherPass', newPass);
                closeSetupPasswordModal();
                alert('✅ ตั้งรหัสผ่านโหมดครูเรียบร้อยแล้ว!');
            }
        }

        function updateClassroomVisibility() {
            document.querySelectorAll('.class-item').forEach(cls => {
                const isJoined = cls.getAttribute('data-joined') === 'true';
                cls.style.display = (currentRole === 'teacher' || isJoined) ? 'flex' : 'none';
            });
        }

        function handleMainAction() {
            if (currentRole === 'teacher') {
                document.getElementById('createModal').classList.remove('hidden');
            } else {
                document.getElementById('joinModal').classList.remove('hidden');
            }
        }
        function closeCreateModal() { document.getElementById('createModal').classList.add('hidden'); }
        function closeJoinModal() { document.getElementById('joinModal').classList.add('hidden'); document.getElementById('joinCodeInput').value = ''; document.getElementById('studentNameInput').value = ''; document.getElementById('studentIdInput').value = ''; }
        function openAssignmentModal() { document.getElementById('assignmentModal').classList.remove('hidden'); }
        function closeAssignmentModal() { document.getElementById('assignmentModal').classList.add('hidden'); }

        function openClassroom(title, code) {
            activeRoomCode = code; 
            document.getElementById('roomTitle').innerText = title;
            document.getElementById('roomCode').innerText = "รหัสห้องเรียน: " + code;
            document.getElementById('modalStudentCount').innerText = classStudents[code] ? classStudents[code].length : 0;
            document.getElementById('teacherControlBox').classList.toggle('hidden', currentRole === 'student');

            renderStreamPosts();
            updateStudentViewStatuses();
            switchRoomTab('stream');
            document.getElementById('roomModal').classList.remove('hidden');
        }
        function closeClassroom() { document.getElementById('roomModal').classList.add('hidden'); }

        function deleteClassroom(code) {
            if (confirm(`คุณต้องการลบห้องเรียนรหัส ${code} ใช่หรือไม่? การกระทำนี้จะไม่สามารถย้อนกลับได้`)) {
                const card = document.querySelector(`.class-item[data-code="${code}"]`);
                if (card) card.remove();

                delete classStudents[code];
                delete submissions[code];
                delete streamPosts[code];
                delete unreadCounts[code];

                addGlobalNotification(`ห้องเรียนรหัส ${code} ถูกลบออกจากระบบแล้ว`);
                alert('🗑️ ลบห้องเรียนสำเร็จ');
            }
        }

        function leaveClassroom(code) {
            if (confirm(`คุณต้องการถอนตัวออกจากห้องเรียนรหัส ${code} ใช่หรือไม่?`)) {
                const card = document.querySelector(`.class-item[data-code="${code}"]`);
                if (card) {
                    card.setAttribute('data-joined', 'false');
                }

                // ลบชื่อนักเรียนออกจากรายชื่อในห้องนั้น
                if (classStudents[code]) {
                    classStudents[code] = classStudents[code].filter(s => s.name !== currentStudentName);
                    const countElem = card.querySelector('.student-count');
                    if (countElem) countElem.innerText = classStudents[code].length;
                }

                updateClassroomVisibility();
                addGlobalNotification(`คุณ ${currentStudentName} ได้ถอนตัวออกจากห้องเรียน ${code} แล้ว`);
                alert('🚪 ถอนตัวออกจากห้องเรียนเรียบร้อยแล้ว');
            }
        }

        function postToStream() {
            const input = document.getElementById('streamPostInput');
            const text = input.value.trim();
            if (!text) return alert('❌ กรุณากรอกข้อความก่อนโพสต์');
            if (currentRole === 'student' && !currentStudentName) return alert('❌ กรุณาลงทะเบียนเข้าห้องเรียนก่อน');

            if (!streamPosts[activeRoomCode]) streamPosts[activeRoomCode] = [];
            const now = new Date();
            const timeStr = `${now.getDate()} ม.ค. ${now.getFullYear() + 543} เวลา ${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')} น.`;

            streamPosts[activeRoomCode].unshift({
                author: currentRole === 'teacher' ? '👩‍🏫 ครูสมชาย ใจดี' : `👤 ${currentStudentName} (${currentStudentId})`,
                role: currentRole,
                time: timeStr,
                text: text
            });

            if (!unreadCounts[activeRoomCode]) unreadCounts[activeRoomCode] = { stream: 0, assignments: 0 };
            unreadCounts[activeRoomCode].stream += 1;
            updateNotificationBadges();
            addGlobalNotification(`มีโพสต์ใหม่ในห้องเรียน (${activeRoomCode})`);

            input.value = '';
            renderStreamPosts();
        }

        function renderStreamPosts() {
            const container = document.getElementById('streamPostsContainer');
            container.innerHTML = '';
            const posts = streamPosts[activeRoomCode] || [];
            if (posts.length === 0) {
                container.innerHTML = '<p class="text-xs text-gray-400 text-center py-6">ยังไม่มีโพสต์หรือประกาศในห้องนี้</p>';
                return;
            }
            posts.forEach(post => {
                const badge = post.role === 'teacher' ? '<span class="bg-indigo-100 text-indigo-800 text-[10px] px-2 py-0.5 rounded-full ml-1 font-bold">📢 ประกาศจากครู</span>' : '<span class="bg-pink-100 text-pink-700 text-[10px] px-2 py-0.5 rounded-full ml-1 font-semibold">💬 โพสต์จากนักเรียน</span>';
                const card = document.createElement('div');
                card.className = "bg-white p-4 rounded-2xl border border-gray-200 shadow-sm";
                card.innerHTML = `<div class="flex justify-between items-center mb-1"><span class="font-bold text-sm text-gray-800">${post.author} ${badge}</span><span class="text-xs text-gray-400">${post.time}</span></div><p class="text-sm text-gray-700 mt-1 whitespace-pre-line">${post.text}</p>`;
                container.appendChild(card);
            });
        }

        function updateStudentViewStatuses() {
            if (currentRole === 'teacher') return;
            
            document.querySelectorAll('.assignment-card').forEach(card => {
                const assignId = card.getAttribute('data-id');
                const statusDiv = card.querySelector(`#status-${assignId}`);
                const uploadBox = card.querySelector(`#uploadBox-${assignId}`);
                const actionBtnBox = card.querySelector(`#actionBtnBox-${assignId}`);
                const feedbackDiv = card.querySelector(`#feedback-${assignId}`);
                
                const subInfo = submissions[activeRoomCode]?.[assignId]?.[currentStudentName];
                
                if (subInfo) {
                    let lateBadge = subInfo.isLate ? '<span class="text-red-500 font-bold ml-1">(⚠️ ส่งช้า)</span>' : '';
                    statusDiv.innerHTML = `<span class="text-green-600 font-bold">✨ สถานะ: ส่งงานแล้ว (${subInfo.time}) ${lateBadge}</span>`;
                    
                    uploadBox.classList.add('hidden');
                    actionBtnBox.innerHTML = `
                        <button onclick="unsubmitAssignment('${assignId}')" class="w-full bg-gray-200 hover:bg-gray-300 text-gray-700 py-1.5 rounded-lg text-xs font-medium transition mb-1">
                            🔄 ยกเลิกการส่งงาน (Unsubmit)
                        </button>
                    `;

                    if (subInfo.grade || subInfo.comment) {
                        feedbackDiv.classList.remove('hidden');
                        feedbackDiv.innerHTML = `
                            <strong>⭐ คะแนน: ${subInfo.grade || 'รอตรวจ'}</strong><br>
                            <span class="text-gray-600">💬 คอมเมนต์ครู: ${subInfo.comment || 'ไม่มี'}</span>
                        `;
                    } else {
                        feedbackDiv.classList.add('hidden');
                    }
                } else {
                    statusDiv.innerHTML = `<span class="text-gray-500 font-semibold">⏳ สถานะ: ยังไม่ได้ส่ง</span>`;
                    uploadBox.classList.remove('hidden');
                    actionBtnBox.innerHTML = `
                        <button onclick="submitAssignment('${assignId}')" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white py-1.5 rounded-lg text-xs font-medium shadow">
                            ส่งงาน (Turn In)
                        </button>
                    `;
                    feedbackDiv.classList.add('hidden');
                }
            });
        }

        function submitAssignment(assignId) {
            if (!currentStudentName) return alert('❌ กรุณาลงทะเบียนเข้าห้องเรียนก่อน');
            const fileInput = document.getElementById(`file-${assignId}`);
            let fileName = "ไม่มีไฟล์แนบ.txt";
            let fileData = null;

            if (fileInput && fileInput.files.length > 0) {
                const file = fileInput.files[0];
                fileName = file.name;
                fileData = URL.createObjectURL(file); 
            }

            if (!submissions[activeRoomCode]) submissions[activeRoomCode] = {};
            if (!submissions[activeRoomCode][assignId]) submissions[activeRoomCode][assignId] = {};

            const now = new Date();
            const timeString = `${now.getDate()} ม.ค. ${now.getFullYear() + 543} เวลา ${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')} น.`;

            const cardElem = document.querySelector(`.assignment-card[data-id="${assignId}"]`);
            let isLate = false;
            if (cardElem) {
                const dueDateTime = new Date(cardElem.getAttribute('data-duedate')).getTime();
                if (now.getTime() > dueDateTime) isLate = true;
            }

            submissions[activeRoomCode][assignId][currentStudentName] = {
                status: 'submitted',
                time: timeString,
                timestamp: now.getTime(),
                fileName: fileName,
                fileData: fileData,
                isLate: isLate,
                grade: '',
                comment: ''
            };

            updateStudentViewStatuses();
            addGlobalNotification(`คุณ ${currentStudentName} (${currentStudentId}) ได้ส่งงานแล้ว`);
            alert('🎉 ส่งงานสำเร็จเรียบร้อยแล้ว!');
        }

        function unsubmitAssignment(assignId) {
            if (confirm('คุณต้องการยกเลิกการส่งงานเพื่อแก้ไขไฟล์ใช่หรือไม่?')) {
                if (submissions[activeRoomCode]?.[assignId]?.[currentStudentName]) {
                    delete submissions[activeRoomCode][assignId][currentStudentName];
                }
                updateStudentViewStatuses();
                alert('🔄 ยกเลิกการส่งงานเรียบร้อยแล้ว');
            }
        }

        function openCheckSubmissionsModal(assignId, assignTitle) {
            activeCheckAssignId = assignId;
            document.getElementById('checkAssignTitle').innerText = "หัวข้องาน: " + assignTitle;
            renderSubmissionsList(assignId);
            document.getElementById('checkSubmissionsModal').classList.remove('hidden');
        }

        function renderSubmissionsList(assignId) {
            const container = document.getElementById('submissionsListContainer');
            container.innerHTML = '';
            const students = classStudents[activeRoomCode] || [];
            if (students.length === 0) {
                container.innerHTML = '<p class="text-xs text-gray-400 text-center py-4">ยังไม่มีนักเรียนลงทะเบียนในห้องนี้</p>';
                return;
            }

            students.forEach((std, index) => {
                const subInfo = submissions[activeRoomCode]?.[assignId]?.[std.name];
                let rightSideContent = '';

                if (subInfo) {
                    let lateBadge = subInfo.isLate ? '<span class="text-red-500 font-bold ml-1">(ส่งช้า)</span>' : '';
                    let gradeText = subInfo.grade ? `<span class="text-indigo-600 font-bold ml-2">[คะแนน: ${subInfo.grade}]</span>` : '';
                    rightSideContent = `
                        <div class="flex items-center space-x-2">
                            <span class="bg-green-100 text-green-700 text-xs px-2 py-1 rounded-full font-bold">✨ ส่งแล้ว ${lateBadge}</span>
                            ${gradeText}
                            <button onclick="openGradeStudentModal('${std.name}', '${assignId}')" class="bg-indigo-600 hover:bg-indigo-700 text-white text-xs px-2.5 py-1 rounded-lg font-medium shadow transition">
                                ✍️ ตรวจและให้คะแนน
                            </button>
                        </div>
                    `;
                } else {
                    rightSideContent = `<span class="bg-red-100 text-red-600 text-xs px-2.5 py-1 rounded-full font-semibold">⏳ ยังไม่ส่ง</span>`;
                }

                const item = document.createElement('div');
                item.className = "bg-white p-3 rounded-xl border border-gray-100 flex justify-between items-center text-sm shadow-sm";
                item.innerHTML = `<span class="font-medium text-gray-800">${index + 1}. [${std.id}] ${std.name}</span> ${rightSideContent}`;
                container.appendChild(item);
            });
        }
        function closeCheckSubmissionsModal() { document.getElementById('checkSubmissionsModal').classList.add('hidden'); }

        function openGradeStudentModal(studentName, assignId) {
            gradingStudentName = studentName;
            activeCheckAssignId = assignId;
            const subInfo = submissions[activeRoomCode][assignId][studentName];

            document.getElementById('gradeModalStudentName').innerText = "นักเรียน: " + studentName;
            document.getElementById('gradeModalFileName').innerText = subInfo.fileName;
            document.getElementById('gradeModalTime').innerText = subInfo.time;
            document.getElementById('inputGradeScore').value = subInfo.grade || '';
            document.getElementById('inputPrivateComment').value = subInfo.comment || '';

            document.getElementById('gradeStudentModal').classList.remove('hidden');
        }
        function closeGradeStudentModal() { document.getElementById('gradeStudentModal').classList.add('hidden'); }

        function saveGradeAndFeedback() {
            const grade = document.getElementById('inputGradeScore').value.trim();
            const comment = document.getElementById('inputPrivateComment').value.trim();

            if (submissions[activeRoomCode]?.[activeCheckAssignId]?.[gradingStudentName]) {
                submissions[activeRoomCode][activeCheckAssignId][gradingStudentName].grade = grade;
                submissions[activeRoomCode][activeCheckAssignId][gradingStudentName].comment = comment;
            }

            closeGradeStudentModal();
            renderSubmissionsList(activeCheckAssignId);
            addGlobalNotification(`คุณครูตรวจงานของ "${gradingStudentName}" เรียบร้อยแล้ว`);
            alert('💾 บันทึกคะแนนและคอมเมนต์สำเร็จ!');
        }

        function openPreviewWork() {
            const subInfo = submissions[activeRoomCode]?.[activeCheckAssignId]?.[gradingStudentName];
            if (!subInfo || !subInfo.fileName) return alert('❌ ไม่พบไฟล์งาน');

            document.getElementById('previewFileNameTitle').innerText = subInfo.fileName;
            const container = document.getElementById('previewContentContainer');
            container.innerHTML = '';

            const ext = subInfo.fileName.split('.').pop().toLowerCase();
            if (['png', 'jpg', 'jpeg', 'gif', 'webp'].includes(ext)) {
                if (subInfo.fileData) {
                    container.innerHTML = `<img src="${subInfo.fileData}" class="max-h-96 rounded-xl shadow border object-contain">`;
                } else {
                    container.innerHTML = `<div class="text-center p-6"><p class="text-sm font-bold text-gray-700">🖼️ ไฟล์ภาพตัวอย่าง (จำลอง)</p><span class="text-xs text-gray-400">${subInfo.fileName}</span></div>`;
                }
            } else if (ext === 'pdf') {
                if (subInfo.fileData) {
                    container.innerHTML = `<iframe src="${subInfo.fileData}" class="w-full h-96 rounded-xl border"></iframe>`;
                } else {
                    container.innerHTML = `<div class="text-center p-6"><p class="text-sm font-bold text-gray-700">📄 เอกสาร PDF ตัวอย่าง</p><span class="text-xs text-gray-400">${subInfo.fileName}</span></div>`;
                }
            } else {
                container.innerHTML = `<div class="text-center p-6"><p class="text-sm font-bold text-gray-700">📁 ไฟล์เอกสารแนบ: ${subInfo.fileName}</p><p class="text-xs text-gray-400 mt-1">ดาวน์โหลดเพื่อตรวจสอบได้</p></div>`;
            }

            document.getElementById('filePreviewModal').classList.remove('hidden');
        }
        function closeFilePreviewModal() { document.getElementById('filePreviewModal').classList.add('hidden'); }

        function openStudentListModal() {
            const container = document.getElementById('studentListContainer');
            container.innerHTML = '';
            const students = classStudents[activeRoomCode] || [];
            if (students.length === 0) {
                container.innerHTML = '<p class="text-xs text-gray-400 text-center py-4">ยังไม่มีนักเรียนลงทะเบียน</p>';
            } else {
                students.forEach((std, idx) => {
                    const item = document.createElement('div');
                    item.className = "bg-white p-2.5 rounded-xl border border-gray-100 flex justify-between items-center text-sm shadow-sm";
                    item.innerHTML = `<span class="font-medium text-gray-800">${idx + 1}. [${std.id}] ${std.name}</span> <span class="bg-green-100 text-green-700 text-xs px-2 py-0.5 rounded-full font-semibold">ลงทะเบียนแล้ว</span>`;
                    container.appendChild(item);
                });
            }
            document.getElementById('studentListModal').classList.remove('hidden');
        }
        function closeStudentListModal() { document.getElementById('studentListModal').classList.add('hidden'); }

        function addClassroom(event) {
            event.preventDefault();
            const name = document.getElementById('className').value;
            const teacher = document.getElementById('teacherName').value;
            const randomCode = 'CLS' + Math.floor(100 + Math.random() * 900);
            
            classStudents[randomCode] = [];
            streamPosts[randomCode] = [];
            unreadCounts[randomCode] = { stream: 0, assignments: 0 };

            const grid = document.getElementById('classroom-grid');
            const newCard = document.createElement('div');
            newCard.className = "glass rounded-2xl shadow-xl overflow-hidden flex flex-col justify-between class-item";
            newCard.setAttribute('data-code', randomCode);
            newCard.setAttribute('data-joined', 'false');
            newCard.innerHTML = `
                <div class="bg-gradient-to-r from-purple-500 to-pink-600 p-6 text-white relative">
                    <h3 class="text-xl font-bold">${name}</h3>
                    <p class="text-purple-100 text-sm mt-1">${teacher}</p>
                    <span class="absolute top-4 right-4 bg-white/20 text-xs px-2.5 py-1 rounded-full">รหัส: ${randomCode}</span>
                </div>
                <div class="p-6 flex-1 flex flex-col justify-between">
                    <div>
                        <div class="flex justify-between text-sm text-gray-600 mb-4">
                            <span>นักเรียนลงทะเบียนแล้ว: <strong class="student-count text-indigo-600">0</strong> คน</span>
                            <span class="text-indigo-600 font-semibold">สถานะ: เปิดอยู่</span>
                        </div>
                    </div>
                    <div class="space-y-2">
                        <button onclick="openClassroom('${name}', '${randomCode}')" class="w-full bg-indigo-50 hover:bg-indigo-100 text-indigo-600 border border-indigo-200 py-2 rounded-xl font-medium text-sm transition">
                            เข้าสู่ห้องเรียน
                        </button>
                        <button onclick="deleteClassroom('${randomCode}')" class="w-full teacher-only-feature bg-red-50 hover:bg-red-100 text-red-600 border border-red-200 py-1.5 rounded-xl font-medium text-xs transition">
                            🗑️ ลบห้องเรียนนี้
                        </button>
                        <button onclick="leaveClassroom('${randomCode}')" class="w-full student-only-feature bg-amber-50 hover:bg-amber-100 text-amber-700 border border-amber-200 py-1.5 rounded-xl font-medium text-xs transition hidden">
                            🚪 ถอนตัวออกจากห้องเรียน
                        </button>
                    </div>
                </div>
            `;
            grid.appendChild(newCard);
            closeCreateModal();
            document.getElementById('className').value = '';
            document.getElementById('teacherName').value = '';
            alert('สร้างห้องเรียนสำเร็จ! รหัสห้องคือ: ' + randomCode);
            updateClassroomVisibility();
        }

        // ระบบลงทะเบียนเข้าห้องเรียน
        function registerAndJoinClassroom(event) {
            event.preventDefault();
            const inputCode = document.getElementById('joinCodeInput').value.trim().toUpperCase();
            const studentId = document.getElementById('studentIdInput').value.trim();
            const studentName = document.getElementById('studentNameInput').value.trim();
            
            let foundCard = null;
            document.querySelectorAll('.class-item').forEach(card => {
                if (card.getAttribute('data-code') === inputCode) foundCard = card;
            });

            if (foundCard) {
                foundCard.setAttribute('data-joined', 'true');
                if (!classStudents[inputCode]) classStudents[inputCode] = [];
                
                const exists = classStudents[inputCode].some(s => s.id === studentId);
                if (!exists) {
                    classStudents[inputCode].push({ id: studentId, name: studentName });
                }

                const countElem = foundCard.querySelector('.student-count');
                if (countElem) countElem.innerText = classStudents[inputCode].length;

                updateClassroomVisibility();
                alert(`🎉 ลงทะเบียนสำเร็จ! ยินดีต้อนรับคุณ "${studentName}" (รหัส: ${studentId}) เข้าสู่ห้องเรียน`);
                closeJoinModal();
                switchRole('student', studentName, studentId);
            } else { alert('❌ รหัสห้องเรียนไม่ถูกต้อง!'); }
        }

        function addAssignment(event) {
            event.preventDefault();
            const title = document.getElementById('assignTitle').value;
            const desc = document.getElementById('assignDesc').value;
            const dateVal = document.getElementById('assignDate').value;
            const timeVal = document.getElementById('assignTime').value;

            let formattedDate = dateVal;
            if (dateVal) {
                const parts = dateVal.split('-');
                formattedDate = `${parseInt(parts[2])} ${["ม.ค.", "ก.พ.", "มี.ค.", "เม.ย.", "พ.ค.", "มิ.ย.", "ก.ค.", "ส.ค.", "ก.ย.", "ต.ค.", "พ.ย.", "ธ.ค."][parseInt(parts[1]) - 1]} ${parseInt(parts[0]) + 543}`;
            }

            const assignId = 'assign-' + Date.now();
            const dueDateTimeStr = `${dateVal}T${timeVal}`;
            const list = document.getElementById('assignment-list');
            
            const newAssign = document.createElement('div');
            newAssign.className = "bg-white p-5 rounded-2xl border border-gray-200 shadow-sm flex flex-col md:flex-row justify-between gap-4 assignment-card";
            newAssign.setAttribute('data-id', assignId);
            newAssign.setAttribute('data-duedate', dueDateTimeStr);
            newAssign.innerHTML = `
                <div class="flex-1">
                    <span class="bg-indigo-100 text-indigo-700 text-xs px-2.5 py-1 rounded-full font-medium">การบ้านใหม่</span>
                    <h3 class="font-bold text-gray-900 text-lg mt-2 assign-title-text">${title}</h3>
                    <p class="text-sm text-gray-600 mt-1">${desc}</p>
                    <p class="text-xs text-red-500 font-semibold mt-3">🕒 กำหนดส่ง: ${formattedDate} เวลา ${timeVal} น.</p>
                    <button onclick="openCheckSubmissionsModal('${assignId}', '${title}')" class="mt-4 teacher-only-feature bg-amber-100 hover:bg-amber-200 text-amber-800 text-xs font-bold px-3 py-1.5 rounded-lg border border-amber-200 transition">
                        📊 ตรวจสถานะและให้คะแนน
                    </button>
                </div>
                <div class="w-full md:w-72 bg-gray-50 p-4 rounded-xl border border-gray-100 flex flex-col justify-between student-submit-box">
                    <div>
                        <h4 class="font-bold text-xs text-gray-700 mb-2">📤 ส่งงานของคุณ</h4>
                        <div id="uploadBox-${assignId}">
                            <input type="file" id="file-${assignId}" accept="image/*,.pdf,.doc,.docx" class="text-xs text-gray-500 file:mr-2 file:py-1.5 file:px-3 file:rounded-lg file:border-0 file:text-xs file:font-semibold file:bg-indigo-50 file:text-indigo-700 mb-2 w-full">
                        </div>
                        <div id="status-${assignId}" class="text-xs font-semibold text-gray-500 mb-2">สถานะ: ยังไม่ได้ส่ง</div>
                        <div id="feedback-${assignId}" class="text-xs bg-indigo-50 p-2 rounded-lg text-indigo-900 hidden mb-2 border border-indigo-100"></div>
                    </div>
                    <div id="actionBtnBox-${assignId}">
                        <button onclick="submitAssignment('${assignId}')" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white py-1.5 rounded-lg text-xs font-medium shadow">
                            ส่งงาน (Turn In)
                        </button>
                    </div>
                </div>
            `;
            list.prepend(newAssign);

            if (!unreadCounts[activeRoomCode]) unreadCounts[activeRoomCode] = { stream: 0, assignments: 0 };
            unreadCounts[activeRoomCode].assignments += 1;
            updateNotificationBadges();
            addGlobalNotification(`มีการบ้านใหม่: "${title}" ในห้องเรียน`);

            closeAssignmentModal();
            document.getElementById('assignTitle').value = '';
            document.getElementById('assignDesc').value = '';
            document.getElementById('assignDate').value = '';
            
            toggleTeacherStudentUI(currentRole === 'teacher');
            alert('สร้างคำสั่งงานสำเร็จ! 📚');
        }
    </script>
</body>
</html>
