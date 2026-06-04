import { useState, useEffect } from "react";

interface AgeResult {
  years: number;
  months: number;
  days: number;
  hours: number;
  minutes: number;
  seconds: number;
  totalDays: number;
  totalHours: number;
  totalMinutes: number;
  totalSeconds: number;
  birthDay: string;
  birthMonth: string;
  birthMonthNum: number;
  birthYear: number;
  zodiacSign: string;
  zodiacEmoji: string;
  zodiacTraits: string[];
  zodiacElement: string;
  zodiacElementEmoji: string;
  zodiacDescription: string;
  nextBirthday: number;
}

const zodiacData: Record<
  string,
  {
    name: string;
    emoji: string;
    element: string;
    elementEmoji: string;
    traits: string[];
    description: string;
  }
> = {
  الحمل: {
    name: "الحمل",
    emoji: "♈",
    element: "النار",
    elementEmoji: "🔥",
    traits: ["شجاع", "متحمس", "قيادي", "مغامر", "واثق بالنفس"],
    description:
      "أنت شخص ديناميكي وجريء، تحب التحديات وتتميز بروح القيادة والإقدام.",
  },
  الثور: {
    name: "الثور",
    emoji: "♉",
    element: "الأرض",
    elementEmoji: "🌍",
    traits: ["صبور", "موثوق", "عملي", "مخلص", "محب للجمال"],
    description:
      "أنت شخص عملي وموثوق، تقدر الاستقرار والجمال وتتمتع بصبر لا حدود له.",
  },
  الجوزاء: {
    name: "الجوزاء",
    emoji: "♊",
    element: "الهواء",
    elementEmoji: "💨",
    traits: ["ذكي", "فضولي", "متكيف", "اجتماعي", "سريع التفكير"],
    description:
      "أنت شخص ذكي ومتعدد المواهب، تحب التواصل وتمتلك قدرة رائعة على التكيف.",
  },
  السرطان: {
    name: "السرطان",
    emoji: "♋",
    element: "الماء",
    elementEmoji: "💧",
    traits: ["عاطفي", "حدسي", "محب", "وفي", "مبدع"],
    description:
      "أنت شخص عاطفي ومحب، لديك حدس قوي وتهتم بمن تحب بشكل عميق.",
  },
  الأسد: {
    name: "الأسد",
    emoji: "♌",
    element: "النار",
    elementEmoji: "🔥",
    traits: ["كريم", "واثق", "مبدع", "دافئ", "طموح"],
    description:
      "أنت شخص كاريزمي ومشرق، تحب أن تكون في مركز الاهتمام وتتمتع بقلب كبير.",
  },
  العذراء: {
    name: "العذراء",
    emoji: "♍",
    element: "الأرض",
    elementEmoji: "🌍",
    traits: ["دقيق", "تحليلي", "مجتهد", "منظم", "متواضع"],
    description:
      "أنت شخص دقيق ومنظم، تتمتع بقدرة تحليلية عالية وتسعى دائماً للكمال.",
  },
  الميزان: {
    name: "الميزان",
    emoji: "♎",
    element: "الهواء",
    elementEmoji: "💨",
    traits: ["عادل", "دبلوماسي", "اجتماعي", "مسالم", "متوازن"],
    description:
      "أنت شخص يحب العدل والتوازن، تتميز بقدرتك على التوسط وحل النزاعات.",
  },
  العقرب: {
    name: "العقرب",
    emoji: "♏",
    element: "الماء",
    elementEmoji: "💧",
    traits: ["مخلص", "شغوف", "حازم", "شجاع", "ثاقب النظر"],
    description:
      "أنت شخص قوي وغامض، لديك إصرار لا يقهر وقدرة عميقة على فهم الآخرين.",
  },
  القوس: {
    name: "القوس",
    emoji: "♐",
    element: "النار",
    elementEmoji: "🔥",
    traits: ["متفائل", "صادق", "مغامر", "فلسفي", "حر"],
    description:
      "أنت شخص مغامر ومتفائل، تحب الحرية والاستكشاف وتسعى دائماً للمعرفة.",
  },
  الجدي: {
    name: "الجدي",
    emoji: "♑",
    element: "الأرض",
    elementEmoji: "🌍",
    traits: ["طموح", "منضبط", "مسؤول", "صبور", "عملي"],
    description:
      "أنت شخص طموح ومنضبط، تعمل بجد لتحقيق أهدافك وتتحمل المسؤولية بكل كفاءة.",
  },
  الدلو: {
    name: "الدلو",
    emoji: "♒",
    element: "الهواء",
    elementEmoji: "💨",
    traits: ["مبتكر", "مستقل", "إنساني", "فريد", "ذكي"],
    description:
      "أنت شخص مبتكر ومستقل الفكر، تهتم بالإنسانية وتمتلك رؤية مستقبلية فريدة.",
  },
  الحوت: {
    name: "الحوت",
    emoji: "♓",
    element: "الماء",
    elementEmoji: "💧",
    traits: ["حساس", "خيالي", "حكيم", "رحيم", "فني"],
    description:
      "أنت شخص حساس وخيالي، لديك قدرة فطرية على التعاطف وموهبة فنية رائعة.",
  },
};

function getZodiacSign(month: number, day: number): string {
  if ((month === 3 && day >= 21) || (month === 4 && day <= 19)) return "الحمل";
  if ((month === 4 && day >= 20) || (month === 5 && day <= 20)) return "الثور";
  if ((month === 5 && day >= 21) || (month === 6 && day <= 20)) return "الجوزاء";
  if ((month === 6 && day >= 21) || (month === 7 && day <= 22)) return "السرطان";
  if ((month === 7 && day >= 23) || (month === 8 && day <= 22)) return "الأسد";
  if ((month === 8 && day >= 23) || (month === 9 && day <= 22)) return "العذراء";
  if ((month === 9 && day >= 23) || (month === 10 && day <= 22)) return "الميزان";
  if ((month === 10 && day >= 23) || (month === 11 && day <= 21)) return "العقرب";
  if ((month === 11 && day >= 22) || (month === 12 && day <= 21)) return "القوس";
  if ((month === 12 && day >= 22) || (month === 1 && day <= 19)) return "الجدي";
  if ((month === 1 && day >= 20) || (month === 2 && day <= 18)) return "الدلو";
  return "الحوت";
}

const arabicMonths = [
  "يناير", "فبراير", "مارس", "إبريل", "مايو", "يونيو",
  "يوليو", "أغسطس", "سبتمبر", "أكتوبر", "نوفمبر", "ديسمبر",
];

const arabicDays = [
  "الأحد", "الاثنين", "الثلاثاء", "الأربعاء", "الخميس", "الجمعة", "السبت",
];

const monthTraits: Record<number, { name: string; trait: string; emoji: string }> = {
  1: { name: "يناير", trait: "ذو إرادة قوية وطموح لا حدود له", emoji: "❄️" },
  2: { name: "فبراير", trait: "رومانسي وعاطفي بطبيعته", emoji: "💝" },
  3: { name: "مارس", trait: "جريء ومفعم بالحيوية", emoji: "🌸" },
  4: { name: "إبريل", trait: "مبدع ومتجدد كالربيع", emoji: "🌺" },
  5: { name: "مايو", trait: "ثابت الرأي ومحب للطبيعة", emoji: "🌻" },
  6: { name: "يونيو", trait: "اجتماعي ومتفائل", emoji: "☀️" },
  7: { name: "يوليو", trait: "مخلص وعاطفي عميق", emoji: "🌊" },
  8: { name: "أغسطس", trait: "واثق وقوي الشخصية", emoji: "🦁" },
  9: { name: "سبتمبر", trait: "منظم وذكي التفكير", emoji: "🍂" },
  10: { name: "أكتوبر", trait: "متوازن ومحب للعدالة", emoji: "🎃" },
  11: { name: "نوفمبر", trait: "عميق التفكير وغامض الشخصية", emoji: "🦂" },
  12: { name: "ديسمبر", trait: "متفائل ومغامر بطبعه", emoji: "⛄" },
};

function calculateAge(birthDate: Date): AgeResult {
  const now = new Date();

  let years = now.getFullYear() - birthDate.getFullYear();
  let months = now.getMonth() - birthDate.getMonth();
  let days = now.getDate() - birthDate.getDate();

  if (days < 0) {
    months--;
    const prevMonth = new Date(now.getFullYear(), now.getMonth(), 0);
    days += prevMonth.getDate();
  }

  if (months < 0) {
    years--;
    months += 12;
  }

  const diffMs = now.getTime() - birthDate.getTime();
  const totalDays = Math.floor(diffMs / (1000 * 60 * 60 * 24));
  const totalHours = Math.floor(diffMs / (1000 * 60 * 60));
  const totalMinutes = Math.floor(diffMs / (1000 * 60));
  const totalSeconds = Math.floor(diffMs / 1000);

  const hours = now.getHours() - birthDate.getHours();
  const minutes = now.getMinutes() - birthDate.getMinutes();
  const seconds = now.getSeconds() - birthDate.getSeconds();

  const birthMonth = birthDate.getMonth() + 1;
  const birthDay = birthDate.getDate();
  const birthYear = birthDate.getFullYear();

  const zodiacSign = getZodiacSign(birthMonth, birthDay);
  const zodiac = zodiacData[zodiacSign];

  const dayOfWeek = birthDate.getDay();
  const birthDayName = arabicDays[dayOfWeek];

  // Next birthday
  const nextBirthday = new Date(
    now.getFullYear(),
    birthDate.getMonth(),
    birthDate.getDate()
  );
  if (nextBirthday <= now) {
    nextBirthday.setFullYear(now.getFullYear() + 1);
  }
  const daysToNextBirthday = Math.ceil(
    (nextBirthday.getTime() - now.getTime()) / (1000 * 60 * 60 * 24)
  );

  return {
    years,
    months,
    days,
    hours: Math.abs(hours),
    minutes: Math.abs(minutes),
    seconds: Math.abs(seconds),
    totalDays,
    totalHours,
    totalMinutes,
    totalSeconds,
    birthDay: birthDayName,
    birthMonth: arabicMonths[birthDate.getMonth()],
    birthMonthNum: birthMonth,
    birthYear,
    zodiacSign,
    zodiacEmoji: zodiac.emoji,
    zodiacTraits: zodiac.traits,
    zodiacElement: zodiac.element,
    zodiacElementEmoji: zodiac.elementEmoji,
    zodiacDescription: zodiac.description,
    nextBirthday: daysToNextBirthday,
  };
}

function CounterCard({
  value,
  label,
  emoji,
  color,
}: {
  value: number | string;
  label: string;
  emoji: string;
  color: string;
}) {
  return (
    <div
      className={`flex flex-col items-center justify-center rounded-2xl p-4 shadow-lg ${color} border border-white/20`}
    >
      <span className="text-3xl mb-1">{emoji}</span>
      <span className="text-3xl font-black text-white drop-shadow">
        {value.toLocaleString("ar-EG")}
      </span>
      <span className="text-xs font-medium text-white/90 mt-1 text-center">
        {label}
      </span>
    </div>
  );
}

export default function App() {
  const [birthDate, setBirthDate] = useState("");
  const [result, setResult] = useState<AgeResult | null>(null);
  const [error, setError] = useState("");
  const [isAnimating, setIsAnimating] = useState(false);
  const [_liveSeconds, setLiveSeconds] = useState(0);
  const [liveMinutes, setLiveMinutes] = useState(0);
  const [liveTotalSeconds, setLiveTotalSeconds] = useState(0);

  // Live counter for seconds
  useEffect(() => {
    if (!result) return;
    const interval = setInterval(() => {
      const now = new Date();
      const birth = new Date(birthDate);
      const diffMs = now.getTime() - birth.getTime();
      setLiveTotalSeconds(Math.floor(diffMs / 1000));
      setLiveMinutes(Math.floor(diffMs / (1000 * 60)));
      setLiveSeconds(now.getSeconds() - birth.getSeconds());
    }, 1000);
    return () => clearInterval(interval);
  }, [result, birthDate]);

  const handleCalculate = () => {
    if (!birthDate) {
      setError("⚠️ يرجى إدخال تاريخ الميلاد");
      return;
    }
    const birth = new Date(birthDate);
    const now = new Date();
    if (birth > now) {
      setError("⚠️ تاريخ الميلاد لا يمكن أن يكون في المستقبل!");
      return;
    }
    if (birth.getFullYear() < 1900) {
      setError("⚠️ يرجى إدخال تاريخ ميلاد صحيح");
      return;
    }
    setError("");
    setIsAnimating(true);
    setTimeout(() => {
      const ageResult = calculateAge(birth);
      setResult(ageResult);
      setLiveTotalSeconds(ageResult.totalSeconds);
      setLiveMinutes(ageResult.totalMinutes);
      setIsAnimating(false);
    }, 600);
  };

  const handleReset = () => {
    setResult(null);
    setBirthDate("");
    setError("");
  };

  const today = new Date().toISOString().split("T")[0];

  return (
    <div
      dir="rtl"
      style={{ fontFamily: "'Tajawal', sans-serif" }}
      className="min-h-screen bg-gradient-to-br from-indigo-950 via-purple-950 to-slate-900 relative overflow-hidden"
    >
      {/* Decorative blobs */}
      <div className="absolute top-0 left-0 w-96 h-96 bg-purple-600/20 rounded-full blur-3xl -translate-x-1/2 -translate-y-1/2 pointer-events-none" />
      <div className="absolute bottom-0 right-0 w-96 h-96 bg-indigo-600/20 rounded-full blur-3xl translate-x-1/2 translate-y-1/2 pointer-events-none" />
      <div className="absolute top-1/2 left-1/2 w-64 h-64 bg-pink-600/10 rounded-full blur-3xl -translate-x-1/2 -translate-y-1/2 pointer-events-none" />

      <div className="relative z-10 min-h-screen flex flex-col items-center py-10 px-4">
        {/* Header */}
        <div className="text-center mb-8">
          <div className="inline-flex items-center justify-center w-20 h-20 rounded-full bg-gradient-to-br from-purple-500 to-indigo-600 shadow-2xl shadow-purple-900/50 mb-4">
            <span className="text-4xl">🎂</span>
          </div>
          <h1 className="text-4xl md:text-5xl font-black text-white mb-2 tracking-tight">
            حاسبة العمر
          </h1>
          <p className="text-purple-300 text-lg font-medium">
            اكتشف سر تاريخ ميلادك ✨
          </p>
        </div>

        {/* Input Card */}
        <div className="w-full max-w-lg bg-white/10 backdrop-blur-md border border-white/20 rounded-3xl p-8 shadow-2xl mb-8">
          <label className="block text-white font-bold text-lg mb-3">
            📅 أدخل تاريخ ميلادك
          </label>
          <input
            type="date"
            value={birthDate}
            onChange={(e) => {
              setBirthDate(e.target.value);
              setError("");
            }}
            max={today}
            className="w-full bg-white/20 border-2 border-white/30 text-white placeholder-white/50 rounded-2xl px-5 py-4 text-xl font-bold focus:outline-none focus:border-purple-400 focus:bg-white/25 transition-all duration-200 cursor-pointer"
            style={{ colorScheme: "dark" }}
          />
          {error && (
            <p className="mt-3 text-red-300 font-bold text-center bg-red-900/30 rounded-xl py-2 px-4">
              {error}
            </p>
          )}
          <div className="flex gap-3 mt-5">
            <button
              onClick={handleCalculate}
              disabled={isAnimating}
              className="flex-1 bg-gradient-to-r from-purple-500 to-indigo-600 hover:from-purple-600 hover:to-indigo-700 text-white font-black text-xl rounded-2xl py-4 shadow-lg shadow-purple-900/50 transition-all duration-200 active:scale-95 disabled:opacity-70"
            >
              {isAnimating ? "⏳ جارٍ الحساب..." : "🔍 احسب عمري"}
            </button>
            {result && (
              <button
                onClick={handleReset}
                className="bg-white/10 hover:bg-white/20 border border-white/20 text-white font-bold text-lg rounded-2xl py-4 px-5 transition-all duration-200 active:scale-95"
              >
                🔄 إعادة
              </button>
            )}
          </div>
        </div>

        {/* Results */}
        {result && !isAnimating && (
          <div className="w-full max-w-2xl space-y-6 animate-fadeIn">

            {/* Main Age */}
            <div className="bg-gradient-to-br from-purple-600/80 to-indigo-700/80 backdrop-blur-md border border-white/20 rounded-3xl p-8 shadow-2xl text-center">
              <p className="text-white/80 text-lg font-medium mb-2">🎉 عمرك الآن</p>
              <div className="flex flex-wrap justify-center items-baseline gap-4">
                <div>
                  <span className="text-7xl font-black text-white drop-shadow-lg">
                    {result.years}
                  </span>
                  <span className="text-2xl font-bold text-purple-200 mr-1">سنة</span>
                </div>
                <div>
                  <span className="text-5xl font-black text-purple-200">
                    {result.months}
                  </span>
                  <span className="text-xl font-bold text-purple-300 mr-1">شهر</span>
                </div>
                <div>
                  <span className="text-5xl font-black text-purple-200">
                    {result.days}
                  </span>
                  <span className="text-xl font-bold text-purple-300 mr-1">يوم</span>
                </div>
              </div>
            </div>

            {/* Birth Info */}
            <div className="bg-white/10 backdrop-blur-md border border-white/20 rounded-3xl p-6 shadow-xl">
              <h2 className="text-white font-black text-xl mb-4 flex items-center gap-2">
                📋 معلومات الميلاد
              </h2>
              <div className="grid grid-cols-3 gap-3">
                <div className="bg-white/10 rounded-2xl p-4 text-center border border-white/10">
                  <span className="text-3xl block mb-1">📅</span>
                  <span className="text-white font-black text-lg">{result.birthDay}</span>
                  <p className="text-white/60 text-xs mt-1">يوم الأسبوع</p>
                </div>
                <div className="bg-white/10 rounded-2xl p-4 text-center border border-white/10">
                  <span className="text-3xl block mb-1">
                    {monthTraits[result.birthMonthNum]?.emoji}
                  </span>
                  <span className="text-white font-black text-lg">{result.birthMonth}</span>
                  <p className="text-white/60 text-xs mt-1">الشهر</p>
                </div>
                <div className="bg-white/10 rounded-2xl p-4 text-center border border-white/10">
                  <span className="text-3xl block mb-1">📆</span>
                  <span className="text-white font-black text-lg">{result.birthYear}</span>
                  <p className="text-white/60 text-xs mt-1">السنة</p>
                </div>
              </div>
              {/* Month trait */}
              <div className="mt-4 bg-gradient-to-r from-pink-500/20 to-purple-500/20 border border-pink-400/20 rounded-2xl px-5 py-3 text-center">
                <span className="text-white/90 font-medium">
                  ✨ أبناء شهر{" "}
                  <span className="font-black text-pink-300">
                    {monthTraits[result.birthMonthNum]?.name}
                  </span>{" "}
                  — {monthTraits[result.birthMonthNum]?.trait}
                </span>
              </div>
            </div>

            {/* Zodiac */}
            <div className="bg-white/10 backdrop-blur-md border border-white/20 rounded-3xl p-6 shadow-xl">
              <h2 className="text-white font-black text-xl mb-4 flex items-center gap-2">
                🌟 برجك الفلكي
              </h2>
              <div className="text-center mb-5">
                <div className="inline-flex items-center justify-center w-24 h-24 rounded-full bg-gradient-to-br from-yellow-400/30 to-orange-500/30 border-2 border-yellow-400/50 mb-3">
                  <span className="text-5xl">{result.zodiacEmoji}</span>
                </div>
                <h3 className="text-4xl font-black text-white mb-1">
                  {result.zodiacSign}
                </h3>
                <div className="flex items-center justify-center gap-2 mb-3">
                  <span className="text-2xl">{result.zodiacElementEmoji}</span>
                  <span className="text-purple-300 font-bold text-lg">
                    عنصر {result.zodiacElement}
                  </span>
                </div>
                <p className="text-white/80 text-base leading-relaxed max-w-sm mx-auto">
                  {result.zodiacDescription}
                </p>
              </div>
              {/* Traits */}
              <div>
                <p className="text-white/70 text-sm font-bold mb-3 text-center">
                  ✅ صفاتك الرئيسية
                </p>
                <div className="flex flex-wrap justify-center gap-2">
                  {result.zodiacTraits.map((trait, i) => (
                    <span
                      key={i}
                      className="bg-gradient-to-r from-purple-500/50 to-indigo-500/50 border border-purple-400/30 text-white font-bold px-4 py-2 rounded-full text-sm shadow"
                    >
                      ⭐ {trait}
                    </span>
                  ))}
                </div>
              </div>
            </div>

            {/* Detailed stats */}
            <div className="bg-white/10 backdrop-blur-md border border-white/20 rounded-3xl p-6 shadow-xl">
              <h2 className="text-white font-black text-xl mb-4 flex items-center gap-2">
                ⏱️ إحصائيات عمرك التفصيلية
              </h2>
              <div className="grid grid-cols-2 md:grid-cols-4 gap-3">
                <CounterCard
                  value={result.totalDays.toLocaleString("ar-EG")}
                  label="إجمالي الأيام"
                  emoji="🗓️"
                  color="bg-gradient-to-br from-blue-500/70 to-blue-700/70"
                />
                <CounterCard
                  value={result.totalHours.toLocaleString("ar-EG")}
                  label="إجمالي الساعات"
                  emoji="🕐"
                  color="bg-gradient-to-br from-emerald-500/70 to-emerald-700/70"
                />
                <CounterCard
                  value={liveMinutes.toLocaleString("ar-EG")}
                  label="إجمالي الدقائق"
                  emoji="⏱️"
                  color="bg-gradient-to-br from-orange-500/70 to-orange-700/70"
                />
                <CounterCard
                  value={liveTotalSeconds.toLocaleString("ar-EG")}
                  label="إجمالي الثواني 🔴"
                  emoji="⚡"
                  color="bg-gradient-to-br from-red-500/70 to-red-700/70"
                />
              </div>
            </div>

            {/* Next birthday */}
            <div className="bg-gradient-to-r from-pink-600/70 to-rose-700/70 backdrop-blur-md border border-pink-400/30 rounded-3xl p-6 shadow-xl text-center">
              <span className="text-5xl block mb-3">🎁</span>
              {result.nextBirthday === 0 ? (
                <>
                  <h3 className="text-white font-black text-2xl mb-1">
                    🎉 عيد ميلادك اليوم!
                  </h3>
                  <p className="text-pink-200 text-base">
                    كل عام وأنت بخير وسعادة! 🥳
                  </p>
                </>
              ) : (
                <>
                  <p className="text-pink-200 font-bold text-lg mb-1">
                    ⏳ باقي على عيد ميلادك القادم
                  </p>
                  <div className="flex items-baseline justify-center gap-2">
                    <span className="text-6xl font-black text-white">
                      {result.nextBirthday}
                    </span>
                    <span className="text-2xl font-bold text-pink-200">يوم</span>
                  </div>
                  <p className="text-pink-300 text-sm mt-2">
                    🎂 استعد للاحتفال بعامك الجديد!
                  </p>
                </>
              )}
            </div>

            {/* Fun facts */}
            <div className="bg-white/10 backdrop-blur-md border border-white/20 rounded-3xl p-6 shadow-xl">
              <h2 className="text-white font-black text-xl mb-4">
                🎯 حقائق ممتعة عنك
              </h2>
              <ul className="space-y-3">
                {[
                  {
                    icon: "🌙",
                    text: `قضيت ما يقارب ${Math.floor(result.totalDays / 3).toLocaleString("ar-EG")} يوماً في النوم`,
                  },
                  {
                    icon: "💓",
                    text: `قلبك نبض حوالي ${(result.totalSeconds * 1.2).toFixed(0).replace(/\B(?=(\d{3})+(?!\d))/g, ",")} مرة منذ ولادتك`,
                  },
                  {
                    icon: "😮‍💨",
                    text: `أخذت حوالي ${(result.totalSeconds * 0.3).toFixed(0).replace(/\B(?=(\d{3})+(?!\d))/g, ",")} نفساً في حياتك`,
                  },
                  {
                    icon: "📚",
                    text: `أمضيت ${Math.floor(result.years / 6) > 0 ? Math.floor(result.years - 6) : 0} سنة في مسيرتك التعليمية تقريباً`,
                  },
                ].map((fact, i) => (
                  <li
                    key={i}
                    className="flex items-center gap-3 bg-white/5 border border-white/10 rounded-2xl px-4 py-3"
                  >
                    <span className="text-2xl">{fact.icon}</span>
                    <span className="text-white/90 font-medium text-base">
                      {fact.text}
                    </span>
                  </li>
                ))}
              </ul>
            </div>

            {/* Footer */}
            <div className="text-center text-purple-400 text-sm pb-6 font-medium">
              ✨ كل لحظة من عمرك ثمينة لا تُقدّر بثمن ✨
            </div>
          </div>
        )}
      </div>
    </div>
  );
}
