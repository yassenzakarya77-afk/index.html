<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Video AI Studio</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-950 text-white min-h-screen p-4 flex flex-col items-center justify-center font-sans">

  <div class="w-full max-w-md bg-gray-900 p-6 rounded-3xl border border-gray-800 shadow-2xl">
    <h1 class="text-xl font-bold text-indigo-400 mb-4">🎬 مولّد الفيديوهات المجاني</h1>

    <textarea id="prompt" rows="3" class="w-full bg-gray-950 border border-gray-800 rounded-xl p-3 text-sm mb-4" placeholder="اكتب الوصف بالإنجليزية: A glowing neon city..."></textarea>
    <input type="password" id="apiKey" class="w-full bg-gray-950 border border-gray-800 rounded-xl p-3 text-sm mb-4" placeholder="مفتاح Hugging Face Token" />

    <button onclick="generateVideo()" id="genBtn" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-3 rounded-xl transition">
      🚀 بدء التوليد
    </button>

    <div id="displayArea" class="mt-6 text-center"></div>
  </div>

  <script>
    async function generateVideo() {
      const prompt = document.getElementById('prompt').value.trim();
      const apiKey = document.getElementById('apiKey').value.trim();
      const displayArea = document.getElementById('displayArea');
      const genBtn = document.getElementById('genBtn');

      if (!prompt || !apiKey) return alert('يرجى ملء جميع الحقول!');

      displayArea.innerHTML = `<div class="animate-spin rounded-full h-8 w-8 border-2 border-indigo-500 border-t-transparent mx-auto"></div><p class="text-xs text-gray-400 mt-2">جاري التوليد (انتظر دقيقة)...</p>`;
      genBtn.disabled = true;

      try {
        const response = await fetch("https://api-inference.huggingface.co/models/ali-vilab/text-to-video-ms-1.7b", {
          method: "POST",
          headers: { "Authorization": `Bearer ${apiKey}`, "Content-Type": "application/json" },
          body: JSON.stringify({ inputs: prompt })
        });

        if (!response.ok) throw new Error('فشل الاتصال، تأكد من المفتاح ورصيد الاستخدام.');

        const blob = await response.blob();
        const videoUrl = URL.createObjectURL(blob);

        displayArea.innerHTML = `
          <p class="text-emerald-400 text-xs mb-2">✨ تم التوليد بنجاح!</p>
          <video src="${videoUrl}" controls autoplay loop class="w-full rounded-2xl border border-gray-800 bg-black"></video>
          <a href="${videoUrl}" download="video.mp4" class="block mt-3 bg-gray-800 text-white py-2 rounded-xl text-xs">⬇️ تحميل الفيديو</a>
        `;
      } catch (err) {
        displayArea.innerHTML = `<p class="text-red-400 text-xs">خطأ: ${err.message}</p>`;
      } finally {
        genBtn.disabled = false;
      }
    }
  </script>
</body>
</html>
