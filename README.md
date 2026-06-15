# Counter-va-Memo-factory-lar-closure-bilan-
// 1. Counter Factory — Qadam, max va min chegaralari bilan
const createCounter = ({ step = 1, min = -Infinity, max = Infinity } = {}) => {
  let count = 0; // Private state

  return {
    increment: () => {
      if (count + step <= max) count += step;
      return count;
    },
    decrement: () => {
      if (count - step >= min) count -= step;
      return count;
    },
    getValue: () => count,
    reset: () => {
      count = 0;
      return count;
    }
  };
};

// 2. Memoize — Funksiya natijalarini keshga saqlash
const memoize = (funk) => {
  const cache = new Map(); // Private kesh obyekti

  return (...args) => {
    const key = JSON.stringify(args); // Argumentlarni kalitga aylantiramiz
    if (cache.has(key)) {
      return cache.get(key); // Keshdan qaytarish
    }
    const result = funk(...args);
    cache.set(key, result); // Keshga saqlash
    return result;
  };
};

// 3. Once — Funksiyani faqat bir marta chaqirilishini ta'minlash
const once = (funk) => {
  let isCalled = false; // Private flag
  let result;           // Private natija

  return (...args) => {
    if (!isCalled) {
      result = funk(...args);
      isCalled = true;
    }
    return result; // Keyingi chaqiriqlarda faqat birinchi natija qaytadi
  };
};

// 4. Debounce — Chaqiriqni belgilangan vaqtga kechiktirish
const debounce = (funk, ms) => {
  let timerId; // Private timer holati

  return (...args) => {
    clearTimeout(timerId);
    timerId = setTimeout(() => {
      funk(...args);
    }, ms);
  };
};

// Oddiy rekurssiv Fibonacci funksiyasi
const fibonacci = (n) => (n <= 1 ? n : fibonacci(n - 1) + fibonacci(n - 2));

// Memoize qilingan (keshlanuvchi) Fibonacci funksiyasi
const memoizedFibonacci = memoize((n) => 
  n <= 1 ? n : memoizedFibonacci(n - 1) + memoizedFibonacci(n - 2)
);

// === TAQQOSLASH TESTI ===
const N = 35; // Test uchun yetarli darajada katta son

// 1. Oddiy Fibonacci tezligini o'lchash
console.time("Oddiy rekurssiya");
const res1 = fibonacci(N);
console.timeEnd("Oddiy rekurssiya");
console.log(`Fibonacci(${N}) = ${res1}\n`);

// 2. Memoize qilingan Fibonacci tezligini o'lchash
console.time("Memoize kesh bilan");
const res2 = memoizedFibonacci(N);
console.timeEnd("Memoize kesh bilan");
console.log(`Memoize_Fibonacci(${N}) = ${res2}\n`);

// 3. Keshni ikkinchi marta chaqirib tekshirish (0ms vaqt olishi kerak)
console.time("Keshdan ikkinchi marta o'qish");
memoizedFibonacci(N);
console.timeEnd("Keshdan ikkinchi marta o'qish");
console.log("=== COUNTER TEST ===");
const counter = createCounter({ step: 5, max: 12, min: -5 });
console.log(counter.increment()); // 5
console.log(counter.increment()); // 10
console.log(counter.increment()); // 10 (Chunki max limit 12, keyingi qadam 15 bo'lolmaydi)
console.log(counter.decrement()); // 5

console.log("\n=== ONCE TEST ===");
const tushum = () => {
  console.log("To'lov qabul qilindi!");
  return 100000;
};
const xavfsizTo'lov = once(tushum);

console.log(xavfsizTo'lov()); // "To'lov qabul qilindi!" -> 100000
console.log(xavfsizTo'lov()); // (Konsolga yozuv chiqmaydi) -> 100000
console.log(xavfsizTo'lov()); // (Konsolga yozuv chiqmaydi) -> 100000

console.log("\n=== DEBOUNCE TEST ===");
const logData = (text) => console.log(`Saqlandi: ${text}`);
const processInput = debounce(logData, 300);

processInput("A");
processInput("Ab");
processInput("Abdu"); // 300ms dan keyin faqat mana shu oxirgisi ishlaydi.
