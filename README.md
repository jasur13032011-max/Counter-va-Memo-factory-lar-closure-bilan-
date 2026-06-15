# Counter-va-Memo-factory-lar-closure-bilan-
// 1. 15+ Mukammal Talabalar Bazasi
const students = [
  { id: 1, name: "Anvar", age: 20, major: "Frontend", score: 85, tags: ["active", "mentor"] },
  { id: 2, name: "Diyora", age: 19, major: "Design", score: 92, tags: ["creative", "top"] },
  { id: 3, name: "Bekzod", age: 22, major: "Backend", score: 74, tags: ["hardworking"] },
  { id: 4, name: "Laylo", age: 21, major: "Frontend", score: 88, tags: ["active", "top"] },
  { id: 5, name: "Sardor", age: 23, major: "Data Science", score: 65, tags: ["researcher"] },
  { id: 6, name: "Madina", age: 18, major: "Design", score: 95, tags: ["top", "young"] },
  { id: 7, name: "Jasur", age: 24, major: "Backend", score: 50, tags: ["borderline"] },
  { id: 8, name: "Guli", age: 20, major: "Frontend", score: 79, tags: ["hardworking"] },
  { id: 9, name: "Farruh", age: 22, major: "Data Science", score: 91, tags: ["top", "mentor"] },
  { id: 10, name: "Zuhra", age: 19, major: "Mobile", score: 83, tags: ["active"] },
  { id: 11, name: "Olim", age: 21, major: "Backend", score: 68, tags: ["researcher"] },
  { id: 12, name: "Nodira", age: 20, major: "Design", score: 87, tags: ["creative"] },
  { id: 13, name: "Shaxzod", age: 25, major: "Mobile", score: 45, tags: ["borderline"] },
  { id: 14, name: "Malika", age: 22, major: "Frontend", score: 94, tags: ["top", "creative"] },
  { id: 15, name: "Rustam", age: 23, major: "Backend", score: 81, tags: ["active"] }
];

// ==========================================
// MUKAMMAL MAP + FILTER ZANJIRLARI (Default qiymatlar funksiya parametrida)
// ==========================================

// Zanjir 1: 80+ ballik Frontendchilarni saralash
const getTopFrontenders = (list = []) => list
  .filter(({ major, score }) => major === "Frontend" && score >= 80)
  .map(({ name }) => name);

// Zanjir 2: "top" talabalarning yoshini xavfsiz (immutable) oshirish
const celebrateTopStudents = (list = []) => list
  .filter(({ tags = [] }) => tags.includes("top"))
  .map(student => ({ ...student, age: student.age + 1 }));

// Zanjir 3: 21 yoshdan katta va balli 70+ bo'lganlar ro'yxati
const getSeniorPassList = (list = []) => list
  .filter(({ age, score }) => age > 21 && score >= 70)
  .map(({ id, name, major }) => ({ id, info: `${name} - ${major}` }));


// ==========================================
// ADVANCED REDUCE METODLARI
// ==========================================

// Reduce 1: Group By Major (To'liq xavfsiz immutable push - nusxa olish bilan)
const groupByMajor = (list = []) => list.reduce((acc, student) => {
  const { major } = student;
  return {
    ...acc,
    [major]: [...(acc[major] ?? []), student] // Nullish coalescing va spread
  };
}, {});

// Reduce 2: Matematik statistika (Count, Total, Average birga)
const getScoreStats = (list = []) => list.reduce((acc, { score }) => {
  const count = acc.count + 1;
  const total = acc.total + score;
  return {
    count,
    total,
    average: Number((total / count).toFixed(2))
  };
}, { count: 0, total: 0, average: 0 });


// ==========================================
// SEARCH METODLARI (FIND, SOME, EVERY)
// ==========================================
const findCriticalStudent = (list = []) => list.find(({ score }) => score < 60) ?? null;
const hasMentors = (list = []) => list.some(({ tags = [] }) => tags.includes("mentor"));
const isEveryonePassing = (list = []) => list.every(({ score }) => score >= 40);


// ==========================================
// TUPLE-PATTERN MULTI-CRITERION SORT (Asl massivni buzmaydi - TO'LIQ IMMUTABLE)
// ==========================================
const sortStudentsAdvanced = (list = []) => [...list].sort((a, b) => {
  // 1-mezon: ball bo'yicha kamayish, 2-mezon: yosh bo'yicha o'sish
  const criteria = [b.score - a.score, a.age - a.age];
  return criteria.find(value => value !== 0) ?? 0;
});
// 1. Counter Factory (Max/Min va qadam chegaralari to'liq tekshirilgan)
const createCounter = ({ step = 1, min = -Infinity, max = Infinity } = {}) => {
  let count = 0;
  return {
    increment: () => (count + step <= max ? (count += step) : count),
    decrement: () => (count - step >= min ? (count -= step) : count),
    getValue: () => count,
    reset: () => (count = 0)
  };
};

// 2. To'liq Xavfsiz Memoize (Kontekst saqlangan holatda)
const memoize = (funk) => {
  const cache = new Map();
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    
    const result = funk.apply(this, args); // 'this' yo'qolmasligi uchun apply
    cache.set(key, result);
    return result;
  };
};

// 3. Bir martalik ijro (Once)
const once = (funk) => {
  let isCalled = false;
  let result;
  return function(...args) {
    if (!isCalled) {
      result = funk.apply(this, args);
      isCalled = true;
    }
    return result;
  };
};

// 4. Haqiqiy Debounce (Taymer to'liq tozalanishi bilan)
const debounce = (funk, ms) => {
  let timerId = null;
  return function(...args) {
    const context = this;
    if (timerId) clearTimeout(timerId);
    timerId = setTimeout(() => {
      funk.apply(context, args);
    }, ms);
  };
};
