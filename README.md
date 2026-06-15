# Counter-va-Memo-factory-lar-closure-bilan-
class EventManager {
  // Private/Class field holatlari
  #status = "Faol";

  constructor(elementIds = []) {
    // Bo'sh massiv uchun xavfsiz boshlang'ich qiymat
    this.elements = elementIds
      .map(id => document.getElementById(id))
      .filter(el => el !== null);

    // 1. Class metodini bind yordamida kontekstga mahkamlash (Xavfsiz o'chirish uchun shart!)
    this.boundHandleClick = this.handleClick.bind(this);
  }

  // ==========================================
  // METOD 1: Oddiy Class Metodi (bind talab qiladi)
  // ==========================================
  handleClick(event) {
    // Agarda bind qilinmasa, bu yerda 'this' undefined (strict mode) bo'lib qoladi
    console.log(`[Metod] Kliklandi: ${event.target.id}. Menejer holati: ${this.#status}`);
  }

  // ==========================================
  // METOD 2: Arrow Class Field (Avtomatik bind bo'ladi)
  // ==========================================
  handleHover = (event) => {
    // Arrow funksiyalar o'z kontekstini yaratmaydi, shuning uchun 'this' har doim EventManager'ga teng
    console.log(`[Arrow] Sichqoncha keldi: ${event.target.id}. Menejer holati: ${this.#status}`);
  }

  // ==========================================
  // EVENT LISTENER'LARNI BOG'LASH
  // ==========================================
  init() {
    this.elements.forEach(element => {
      // Bound qilingan metodni va Arrow metodni xavfsiz bog'laymiz
      element.addEventListener("click", this.boundHandleClick);
      element.addEventListener("mouseenter", this.handleHover);
    });

    // Asinxron taymerlarda 'this'ni yo'qotmaslik (Arrow va Bind misoli)
    this.startStatusLog();
  }

  // ==========================================
  // SETTIMEOUT / SETINTERVAL ICHIDA THIS
  // ==========================================
  startStatusLog() {
    // Muammo: Oddiy funksiya ichida 'this' Window bo'lib qoladi.
    // Yechim 1: Arrow funksiya ishlatish (Konteksni tashqaridan oladi)
    setTimeout(() => {
      console.log(`[Timer Arrow] Tizim holati: ${this.#status}`);
    }, 1000);

    // Yechim 2: Eksplicit bind qilish
    setInterval(function() {
      console.log(`[Timer Bind] Tizim tekshiruvi: ${this.#status}`);
    }.bind(this), 5000);
  }

  // ==========================================
  // MANUAL HANDLER REMOVE (Xavfsiz olib tashlash)
  // ==========================================
  destroy() {
    this.elements.forEach(element => {
      // Diqqat: element.addEventListener("click", this.handleClick.bind(this)) deb yozilsa,
      // removeEventListener uni o'chira olmaydi, chunki bind har safar YANGI funksiya qaytaradi.
      // Shuning uchun constructor'da saqlangan 'this.boundHandleClick' moslamasini o'chiramiz:
      element.removeEventListener("click", this.boundHandleClick);
      
      // Arrow field'lar esa o'zgaruvchi hisoblangani uchun to'g'ridan-to'g'ri o'chadi
      element.removeEventListener("mouseenter", this.handleHover);
    });
    console.log("Menejer tozalindi va xotira bo'shatildi.");
  }
}
// Kontekst uchun alohida tashqi obyektlar
const loggerService = { prefix: "[LOG]" };
const alertService = { prefix: "[ALERT]" };

// Universal xabar chiqarish funksiyasi
function displayMessage(type, message) {
  console.log(`${this.prefix} - Type: ${type}, Message: ${message}`);
}

// ---------------------------------------------------------
// 1. CALL Bilan Chaqirish (Argumentlar ketma-ket beriladi)
// ---------------------------------------------------------
displayMessage.call(loggerService, "Info", "Tizim muvaffaqiyatli yuklandi.");

// ---------------------------------------------------------
// 2. APPLY Bilan Chaqirish (Argumentlar massiv ko'rinishida beriladi)
// ---------------------------------------------------------
const alertArgs = ["Warning", "Xotira to'lib bormoqda!"];
displayMessage.apply(alertService, alertArgs);

// ---------------------------------------------------------
// 3. PARTIAL APPLICATION (Bind bilan argumentni muzlatish)
// ---------------------------------------------------------
// Birinchi argumentni ("Error") har doim qat'iy qilib biriktirib qo'yamiz
const logError = displayMessage.bind(loggerService, "Error");

// Endi logError faqat bitta argument (message) qabul qiladi
logError("Baza bilan aloqa uzildi!"); // Natija: [LOG] - Type: Error, Message: Baza bilan aloqa uzildi!
logError("Avtorizatsiya xatosi.");    // Natija: [LOG] - Type: Error, Message: Avtorizatsiya xatosi.
