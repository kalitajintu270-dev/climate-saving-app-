import { useState, useEffect } from 'react';
import {
  Zap,
  Activity,
  Sparkles,
  Lock,
  Unlock,
  Check,
  Calendar,
  ArrowRight,
  Play,
  Pause,
  RefreshCw,
  Trophy,
  Award,
  ChefHat,
  Leaf,
  CheckCircle2,
  Calendar as CalendarIcon,
  ShieldAlert,
  Sliders,
  MousePointerClick
} from 'lucide-react';

// Interfaces
interface Ingredient {
  id: string;
  name: string;
  protein: number;
  carbs: number;
  fat: number;
  calories: number;
  category: 'base' | 'topping' | 'sauce';
  icon?: string;
  desc?: string;
}

interface GoalTheme {
  id: 'muscle' | 'weight' | 'energy' | 'endurance';
  name: string;
  tagline: string;
  badge: string;
  quote: string;
  colorClass: string; // Tailwind color theme
  gradientFromTo: string;
  accentBorder: string;
  accentBg: string;
  accentText: string;
  recommendedBase: string; // ingredient ID
  recommendedSauce: string; // ingredient ID
  recommendedToppings: string[]; // ingredient IDs
}

// Hardcoded Ingredients Database
const INGREDIENTS: Ingredient[] = [
  // Bases
  { id: 'quinoa', name: 'Organic Quinoa Base', protein: 5, carbs: 30, fat: 3, calories: 170, category: 'base', icon: '🌾', desc: 'High-fiber, complete amino acid profile' },
  { id: 'brown_rice', name: 'Steamed Brown Rice', protein: 4, carbs: 35, fat: 1.5, calories: 180, category: 'base', icon: '🍚', desc: 'Slow-release complex carbohydrates' },
  { id: 'millet', name: 'Pearl Millet (Bajra)', protein: 4, carbs: 32, fat: 2, calories: 160, category: 'base', icon: '🌾', desc: 'Ancient grain, highly alkaline and mineral-rich' },
  { id: 'spinach', name: 'Fresh Baby Spinach', protein: 2, carbs: 3, fat: 0.5, calories: 25, category: 'base', icon: '🍃', desc: 'Low-carb, high iron & volume booster' },
  { id: 'cauli_rice', name: 'Keto Cauliflower Rice', protein: 2, carbs: 4, fat: 0.5, calories: 30, category: 'base', icon: '🥦', desc: 'Extremely light, perfect for shredding' },
  { id: 'sweet_potato', name: 'Roasted Sweet Potato Mash', protein: 3, carbs: 28, fat: 0.5, calories: 130, category: 'base', icon: '🍠', desc: 'Rapid glycogen replenishment, beta-carotene rich' },

  // Toppings
  { id: 'tofu', name: 'Organic Grilled Tofu Cubes', protein: 8, carbs: 2, fat: 4, calories: 80, category: 'topping', icon: '⬜', desc: 'Lean plant protein soaked in herbal marinade' },
  { id: 'paneer', name: 'Low-Fat Fresh Paneer', protein: 7, carbs: 2, fat: 6, calories: 90, category: 'topping', icon: '🧀', desc: 'Slow-digesting casein, rich in calcium' },
  { id: 'pumpkin_seeds', name: 'Raw Pumpkin Seeds', protein: 4, carbs: 3, fat: 6, calories: 80, category: 'topping', icon: '🌻', desc: 'Packed with zinc and magnesium' },
  { id: 'almonds', name: 'Sliced Roasted Almonds', protein: 3, carbs: 3, fat: 7, calories: 90, category: 'topping', icon: '🥜', desc: 'Healthy monounsaturated fats for cognitive health' },
  { id: 'chia_hemp', name: 'Hemp & Chia Seed Blast', protein: 4, carbs: 2, fat: 5, calories: 70, category: 'topping', icon: '🌱', desc: 'Omega-3 fatty acids & perfect fiber' },
  { id: 'avocado', name: 'Fresh Hass Avocado Slices', protein: 1, carbs: 6, fat: 11, calories: 120, category: 'topping', icon: '🥑', desc: 'Creamy high-density cellular fuel' },
  { id: 'sprouts', name: 'Sprouted Alfaalfa & Moong', protein: 4, carbs: 4, fat: 0.2, calories: 35, category: 'topping', icon: '🌿', desc: 'Live enzymes, highly bioavailable nutrients' },
  { id: 'mushrooms', name: 'Sautéed Garlic Mushrooms', protein: 2, carbs: 3, fat: 0.5, calories: 30, category: 'topping', icon: '🍄', desc: 'Rich in vitamin D and immunity-boosting beta-glucans' },

  // Sauces
  { id: 'spicy_mayo', name: 'Spicy Hustle Light Mayo', protein: 1, carbs: 2, fat: 8, calories: 90, category: 'sauce', icon: '🌶️', desc: 'Smoky, spicy kick without the heavy sugar' },
  { id: 'mint_punch', name: 'Mint Cilantro Punch', protein: 1, carbs: 2, fat: 0.5, calories: 15, category: 'sauce', icon: '🟢', desc: 'Cooling, digestion-boosting traditional blend' },
  { id: 'garlic_herb', name: 'Garlic Herb Power Emulsion', protein: 1, carbs: 3, fat: 6, calories: 70, category: 'sauce', icon: '🧄', desc: 'Heart-healthy allium booster and fresh herbs' },
  { id: 'tamarind_fuel', name: 'Tangy Tamarind Energy Fuel', protein: 0.5, carbs: 8, fat: 0.5, calories: 40, category: 'sauce', icon: '🟤', desc: 'Sweet-sour traditional extract with antioxidant load' },
  { id: 'citrus_glaze', name: 'Zesty Lemon Citrus Drizzle', protein: 0, carbs: 5, fat: 0, calories: 20, category: 'sauce', icon: '🍋', desc: 'Ultra-light, refreshing metabolism spike' }
];

// Fixed Core Protein Mix (Automatically in every bowl)
const CORE_PROTEIN_MIX = {
  name: 'Core HealthXpress Mix (Peanut + Moong + Rajma)',
  protein: 15,
  carbs: 22,
  fat: 1.5,
  calories: 160,
  desc: 'The legendary organic signature trio: Roasted unsalted peanuts, sprouted green moong, and rich high-altitude kidney beans (rajma) slow-steamed to perfection. The Peanut–Moong–Rajma synergy unlocks a complete plant-amino profile.'
};

// Custom HX Brand Logo Component (SVG version of the official HealthXpress logo)
const HXLogo = ({ className = '' }: { className?: string }) => (
  <svg viewBox="0 0 100 100" className={className} xmlns="http://www.w3.org/2000/svg">
    {/* Outer green ring */}
    <circle cx="50" cy="50" r="46" fill="none" stroke="#10b981" strokeWidth="3" />
    {/* Inner gold ring */}
    <circle cx="50" cy="50" r="40" fill="none" stroke="#f59e0b" strokeWidth="1.5" />
    {/* Sun rays above HX */}
    <g stroke="#f59e0b" strokeWidth="2" strokeLinecap="round">
      <line x1="50" y1="18" x2="50" y2="12" />
      <line x1="42" y1="20" x2="40" y2="14" />
      <line x1="58" y1="20" x2="60" y2="14" />
      <line x1="36" y1="24" x2="32" y2="19" />
      <line x1="64" y1="24" x2="68" y2="19" />
    </g>
    {/* Sun arc/swoosh */}
    <path d="M 32 32 Q 50 24 68 32" stroke="#f59e0b" strokeWidth="2.5" fill="none" strokeLinecap="round" />
    {/* H letter */}
    <text x="33" y="58" fontFamily="Arial Black, sans-serif" fontSize="22" fontWeight="900" fill="#f59e0b">H</text>
    {/* X letter */}
    <text x="50" y="58" fontFamily="Arial Black, sans-serif" fontSize="22" fontWeight="900" fill="#f59e0b">X</text>
    {/* Bottom green banner */}
    <path d="M 22 70 L 78 70 L 75 78 L 25 78 Z" fill="#10b981" />
    <text x="50" y="76" fontFamily="Arial, sans-serif" fontSize="6" fontWeight="900" fill="#ffffff" textAnchor="middle">30G PROTEIN</text>
    {/* Bottom dot indicators */}
    <circle cx="40" cy="84" r="1.5" fill="#fbbf24" />
    <circle cx="50" cy="84" r="1.5" fill="#10b981" />
    <circle cx="60" cy="84" r="1.5" fill="#dc2626" />
  </svg>
);

// Goal Themes Configuration
const GOAL_THEMES: Record<string, GoalTheme> = {
  muscle: {
    id: 'muscle',
    name: 'Muscle Gain & Power',
    tagline: 'ENGINEERED FOR ELITE HYPERTROPHY',
    badge: 'BUILDER MODE',
    quote: 'Maximize amino-acid synthesis. Formulated to drive continuous recovery and fuel heavy lifts.',
    colorClass: 'orange',
    gradientFromTo: 'from-orange-600 via-orange-500 to-red-600',
    accentBorder: 'border-orange-500',
    accentBg: 'bg-orange-500',
    accentText: 'text-orange-400',
    recommendedBase: 'brown_rice',
    recommendedSauce: 'spicy_mayo',
    recommendedToppings: ['paneer', 'pumpkin_seeds', 'tofu']
  },
  weight: {
    id: 'weight',
    name: 'Weight Loss & Shred',
    tagline: 'HIGH-SATIETY KETO-FRIENDLY FORMULA',
    badge: 'SHREDDER MODE',
    quote: 'Melt fat stores while sparing lean muscle structure. High volume fiber paired with 30G pure, clean protein.',
    colorClass: 'emerald',
    gradientFromTo: 'from-emerald-500 via-teal-500 to-cyan-600',
    accentBorder: 'border-emerald-500',
    accentBg: 'bg-emerald-500',
    accentText: 'text-emerald-400',
    recommendedBase: 'spinach',
    recommendedSauce: 'mint_punch',
    recommendedToppings: ['tofu', 'sprouts', 'mushrooms']
  },
  energy: {
    id: 'energy',
    name: 'Sustained Energy & Focus',
    tagline: 'CODER & DEEP WORK SUPERFUEL',
    badge: 'DEEP WORK FOCUS',
    quote: 'Zero glucose spikes, zero afternoon crashes. Engineered with neuro-healthy fats and slow-burning grains.',
    colorClass: 'indigo',
    gradientFromTo: 'from-indigo-600 via-violet-500 to-purple-600',
    accentBorder: 'border-indigo-500',
    accentBg: 'bg-indigo-500',
    accentText: 'text-indigo-400',
    recommendedBase: 'quinoa',
    recommendedSauce: 'garlic_herb',
    recommendedToppings: ['almonds', 'chia_hemp', 'avocado']
  },
  endurance: {
    id: 'endurance',
    name: 'Endurance & Stamina',
    tagline: 'LONG-CHAIN GLYCOGEN LOADING',
    badge: 'ATHLETE MODE',
    quote: 'Keep pushing your physical limits. Sustained complex carbs and electrolytes for uninterrupted mileage.',
    colorClass: 'amber',
    gradientFromTo: 'from-amber-500 via-yellow-500 to-orange-600',
    accentBorder: 'border-amber-500',
    accentBg: 'bg-amber-500',
    accentText: 'text-amber-400',
    recommendedBase: 'sweet_potato',
    recommendedSauce: 'citrus_glaze',
    recommendedToppings: ['pumpkin_seeds', 'sprouts', 'avocado']
  }
};

// Initial Leaderboard Data
const INITIAL_LEADERBOARD = [
  { rank: 1, name: 'DeepWorkDev', goal: 'Sustained Energy', totalProtein: 1450, hoursLogged: 120, isUser: false },
  { rank: 2, name: 'GymBeast99', goal: 'Muscle Gain', totalProtein: 1380, hoursLogged: 95, isUser: false },
  { rank: 3, name: 'QuinoaQueen', goal: 'Weight Loss', totalProtein: 1320, hoursLogged: 110, isUser: false },
  { rank: 4, name: 'ReactRacer', goal: 'Sustained Energy', totalProtein: 1260, hoursLogged: 104, isUser: false },
  { rank: 5, name: 'PeanutPower', goal: 'Endurance', totalProtein: 1190, hoursLogged: 88, isUser: false },
  { rank: 6, name: 'IronHustler', goal: 'Muscle Gain', totalProtein: 1110, hoursLogged: 76, isUser: false },
  { rank: 7, name: 'SproutSpeedster', goal: 'Endurance', totalProtein: 1020, hoursLogged: 98, isUser: false },
  { rank: 8, name: 'HustlingHacker (You)', goal: 'Sustained Energy', totalProtein: 930, hoursLogged: 82, isUser: true },
  { rank: 9, name: 'KetoCoder', goal: 'Weight Loss', totalProtein: 870, hoursLogged: 70, isUser: false },
  { rank: 10, name: 'ProteinPioneer', goal: 'Muscle Gain', totalProtein: 790, hoursLogged: 64, isUser: false }
];

export default function App() {
  // --- STATE ---
  const [showQuiz, setShowQuiz] = useState<boolean>(true);
  const [quizStep, setQuizStep] = useState<number>(1);
  const [quizAnswers, setQuizAnswers] = useState({
    dailyGrind: 'Coding/Desk',
    fitnessGoal: 'energy' as keyof typeof GOAL_THEMES,
    allergies: 'None'
  });

  // Active theme based on personalization
  const [activeGoal, setActiveGoal] = useState<keyof typeof GOAL_THEMES>('energy');
  const currentTheme = GOAL_THEMES[activeGoal];

  // Customizer State
  const [selectedBase, setSelectedBase] = useState<string>('quinoa');
  const [selectedSauce, setSelectedSauce] = useState<string>('garlic_herb');
  const [selectedToppings, setSelectedToppings] = useState<string[]>(['almonds', 'chia_hemp']);
  const [macroLock, setMacroLock] = useState<boolean>(true);

  // Loyalty Program State
  const [streakCount, setStreakCount] = useState<number>(2); // Starts at 2 months
  const [showFlaskCelebration, setShowFlaskCelebration] = useState<boolean>(false);
  const [subscriptionDelivered, setSubscriptionDelivered] = useState<boolean>(false);

  // Leaderboard & Challenge State
  const [leaderboard, setLeaderboard] = useState(INITIAL_LEADERBOARD);
  const [userLoggedToday, setUserLoggedToday] = useState<boolean>(false);
  const [challengeProgress, setChallengeProgress] = useState<number>(65); // 65% complete in 30-day challenge

  // Seed to Spoon Hub State
  const [activeVideo, setActiveVideo] = useState<'qc' | 'farm' | 'kitchen'>('qc');
  const [isPlaying, setIsPlaying] = useState<boolean>(true);

  // Pause / Skip Calendar State (8 Weeks)
  const [calendarWeeks, setCalendarWeeks] = useState([
    { id: 1, dateRange: 'Oct 05 - Oct 11', status: 'scheduled', label: 'Delivery #1 - Standard' },
    { id: 2, dateRange: 'Oct 12 - Oct 18', status: 'scheduled', label: 'Delivery #2 - Standard' },
    { id: 3, dateRange: 'Oct 19 - Oct 25', status: 'scheduled', label: 'Delivery #3 - Standard' },
    { id: 4, dateRange: 'Oct 26 - Nov 01', status: 'scheduled', label: 'Delivery #4 - Standard' },
    { id: 5, dateRange: 'Nov 02 - Nov 08', status: 'scheduled', label: 'Delivery #5 - Standard' },
    { id: 6, dateRange: 'Nov 09 - Nov 15', status: 'paused', label: 'Traveling ✈️' },
    { id: 7, dateRange: 'Nov 16 - Nov 22', status: 'scheduled', label: 'Delivery #6 - Standard' },
    { id: 8, dateRange: 'Nov 23 - Nov 29', status: 'scheduled', label: 'Delivery #7 - Standard' }
  ]);

  // Hustler Calendar Delivery Schedule State
  const [calendarSync, setCalendarSync] = useState<boolean>(true);
  const [deliveryAroundFocus, setDeliveryAroundFocus] = useState<boolean>(true);
  const [focusBlocks] = useState([
    { id: '1', title: 'Daily Standup Meeting', start: '09:30 AM', end: '10:00 AM', isFocus: false },
    { id: '2', title: 'Deep Work Coding Block', start: '11:00 AM', end: '01:00 PM', isFocus: true },
    { id: '3', title: 'Product Architecture Review', start: '03:00 PM', end: '04:30 PM', isFocus: true }
  ]);
  const [deliveryTimeText, setDeliveryTimeText] = useState<string>('10:45 AM (15m before your Focus Block 🚀)');

  // Notification Feed
  const [notification, setNotification] = useState<string | null>(
    '⚡ Personalization engine loaded: Dashboard customized for focus, energy, and cognitive performance.'
  );

  // Auto-set the recommended bowl when the active goal changes
  useEffect(() => {
    const recommended = GOAL_THEMES[activeGoal];
    if (recommended) {
      setSelectedBase(recommended.recommendedBase);
      setSelectedSauce(recommended.recommendedSauce);
      setSelectedToppings(recommended.recommendedToppings);
      
      // Flash notification
      showToast(`🎯 Switched theme to ${recommended.name}. Ingredients and visual interface updated!`);
    }
  }, [activeGoal]);

  // Handle Macro Lock logic
  // Core protein has 15g. We want precisely 30G.
  // When Macro Lock is ON, if base + toppings + sauce protein does not equal exactly 15g (to make total 30g),
  // we dynamically tweak ingredient ratios/toppings to lock total protein at exactly 30g!
  const calculateMacros = () => {
    let baseObj = INGREDIENTS.find(i => i.id === selectedBase);
    let sauceObj = INGREDIENTS.find(i => i.id === selectedSauce);
    let toppingObjs = INGREDIENTS.filter(i => selectedToppings.includes(i.id));

    let proteinSum = CORE_PROTEIN_MIX.protein + (baseObj?.protein || 0) + (sauceObj?.protein || 0) + 
                     toppingObjs.reduce((acc, curr) => acc + curr.protein, 0);
    
    let carbsSum = CORE_PROTEIN_MIX.carbs + (baseObj?.carbs || 0) + (sauceObj?.carbs || 0) + 
                   toppingObjs.reduce((acc, curr) => acc + curr.carbs, 0);

    let fatSum = CORE_PROTEIN_MIX.fat + (baseObj?.fat || 0) + (sauceObj?.fat || 0) + 
                 toppingObjs.reduce((acc, curr) => acc + curr.fat, 0);

    let caloriesSum = CORE_PROTEIN_MIX.calories + (baseObj?.calories || 0) + (sauceObj?.calories || 0) + 
                      toppingObjs.reduce((acc, curr) => acc + curr.calories, 0);

    let isAutoAdjusted = false;
    let adjustmentMsg = '';

    if (macroLock) {
      const targetProteinFromCustoms = 15; // 30g target - 15g core = 15g customs
      let currentCustomsProtein = (baseObj?.protein || 0) + (sauceObj?.protein || 0) + 
                                  toppingObjs.reduce((acc, curr) => acc + curr.protein, 0);
      
      if (currentCustomsProtein !== targetProteinFromCustoms) {
        isAutoAdjusted = true;
        const diff = targetProteinFromCustoms - currentCustomsProtein;
        // Adjust values perfectly to maintain exactly 30G
        // We simulate this by adjusting portions: we add/subtract proteins and label it as optimized
        proteinSum = 30;
        // Adjust calories, carbs, fat proportionally for the locked amount
        caloriesSum += Math.round(diff * 12);
        carbsSum += Math.round(diff * 1.5);
        fatSum += Math.round(diff * 0.5);
        
        if (diff > 0) {
          adjustmentMsg = `Portions micro-boosted: Added +${diff}g bioavailable protein (optimized Tofu/Paneer ratios) to secure exactly 30G!`;
        } else {
          adjustmentMsg = `Portions optimized down: Scaled down excess seed fats by ${Math.abs(diff)}g protein to lock precisely at 30G!`;
        }
      }
    }

    return {
      protein: proteinSum,
      carbs: carbsSum,
      fat: fatSum,
      calories: caloriesSum,
      isAutoAdjusted,
      adjustmentMsg
    };
  };

  const macros = calculateMacros();

  // Helper to trigger toast notifications
  const showToast = (msg: string) => {
    setNotification(msg);
    setTimeout(() => {
      setNotification((prev) => (prev === msg ? null : prev));
    }, 6000);
  };

  // Handle Onboarding Completion
  const handleCompleteQuiz = () => {
    setActiveGoal(quizAnswers.fitnessGoal);
    setShowQuiz(false);
    showToast(`🚀 Personalization loaded! Website transformed for "${GOAL_THEMES[quizAnswers.fitnessGoal].name}" goal!`);
  };

  // Toggle Toppings in customizer
  const handleToggleTopping = (id: string) => {
    if (selectedToppings.includes(id)) {
      setSelectedToppings(selectedToppings.filter(t => t !== id));
    } else {
      setSelectedToppings([...selectedToppings, id]);
    }
  };

  // Simulate Monthly Delivery for Streak Progression
  const triggerDeliverySimulation = () => {
    if (subscriptionDelivered) {
      showToast('📦 Subscription already delivered for this billing window. Next package generates on Nov 1st!');
      return;
    }
    setSubscriptionDelivered(true);
    const newStreak = streakCount + 1;
    setStreakCount(newStreak);

    if (newStreak === 3) {
      setTimeout(() => {
        setShowFlaskCelebration(true);
      }, 800);
    } else {
      showToast(`📦 Delivery #3 successfully fulfilled! Hustle Streak updated to ${newStreak} Months! 🔥`);
    }
  };

  // Submit Today's Fuel Stats to climb leaderboard
  const handleLogFuel = () => {
    if (userLoggedToday) {
      showToast('💪 Fuel logged! Your stats are already locked in for today. Keep hacking!');
      return;
    }
    setUserLoggedToday(true);
    
    // Update leaderboard data
    setLeaderboard(prev => {
      return prev.map(player => {
        if (player.isUser) {
          const updatedProtein = player.totalProtein + 30;
          const updatedHours = player.hoursLogged + 8;
          return {
            ...player,
            totalProtein: updatedProtein,
            hoursLogged: updatedHours,
            rank: 4 // Move user up from rank 8 to 4
          };
        }
        return player;
      }).sort((a, b) => b.totalProtein - a.totalProtein)
        .map((player, index) => ({ ...player, rank: index + 1 }));
    });

    setChallengeProgress(prev => Math.min(prev + 3, 100));
    showToast('🏆 Heavy gains! You logged 30G Protein & 8 Focus Hours. Your leaderboard rank surged from #8 to #4! 🚀');
  };

  // Single Click Skip/Pause Calendar Toggle
  const handleToggleWeek = (id: number) => {
    setCalendarWeeks(prev =>
      prev.map(w => {
        if (w.id === id) {
          const nextStatus = w.status === 'scheduled' ? 'paused' : 'scheduled';
          const nextLabel = nextStatus === 'paused' ? 'Paused - Skipping ✈️' : 'Delivery Scheduled';
          showToast(`🗓️ Week of ${w.dateRange} changed to ${nextStatus.toUpperCase()}. Subscription bill automatically updated.`);
          return { ...w, status: nextStatus, label: nextLabel };
        }
        return w;
      })
    );
  };

  // Dynamic cost calculation based on active weeks
  const activeWeeksCount = calendarWeeks.filter(w => w.status === 'scheduled').length;
  const baseCostPerBowl = 14.50;
  const deliveryFee = 2.50;
  const calculatedMonthlyPrice = activeWeeksCount * baseCostPerBowl + (activeWeeksCount > 0 ? deliveryFee : 0);

  // Recalculate focus block delivery
  useEffect(() => {
    if (deliveryAroundFocus) {
      // Find the first intense focus block
      const mainFocus = focusBlocks.find(b => b.isFocus);
      if (mainFocus) {
        setDeliveryTimeText(`10:45 AM (Pre-focus fuel: 15m before your "${mainFocus.title}" 🧠)`);
      } else {
        setDeliveryTimeText('12:00 PM (Standard Noon window)');
      }
    } else {
      setDeliveryTimeText('12:15 PM (Interrupt-blind standard delivery)');
    }
  }, [deliveryAroundFocus, focusBlocks]);

  // Theme color maps for dynamic classes
  const getThemeClasses = (type: string) => {
    switch (type) {
      case 'muscle':
        return {
          glow: 'shadow-orange-950/40 border-orange-500/30 text-orange-400',
          bgLight: 'bg-orange-500/10',
          borderAccent: 'border-orange-500',
          textAccent: 'text-orange-500',
          buttonClass: 'bg-gradient-to-r from-orange-600 to-red-600 hover:from-orange-500 hover:to-red-500 text-white shadow-lg shadow-orange-600/30',
          pillBg: 'bg-orange-600/20 text-orange-300 border-orange-500/30',
          cardBorder: 'hover:border-orange-500/50 border-zinc-800'
        };
      case 'weight':
        return {
          glow: 'shadow-emerald-950/40 border-emerald-500/30 text-emerald-400',
          bgLight: 'bg-emerald-500/10',
          borderAccent: 'border-emerald-500',
          textAccent: 'text-emerald-500',
          buttonClass: 'bg-gradient-to-r from-emerald-500 to-teal-500 hover:from-emerald-400 hover:to-teal-400 text-white shadow-lg shadow-emerald-600/30',
          pillBg: 'bg-emerald-600/20 text-emerald-300 border-emerald-500/30',
          cardBorder: 'hover:border-emerald-500/50 border-zinc-800'
        };
      case 'endurance':
        return {
          glow: 'shadow-amber-950/40 border-amber-500/30 text-amber-400',
          bgLight: 'bg-amber-500/10',
          borderAccent: 'border-amber-500',
          textAccent: 'text-amber-500',
          buttonClass: 'bg-gradient-to-r from-amber-500 to-yellow-600 hover:from-amber-400 hover:to-yellow-500 text-white shadow-lg shadow-amber-600/30',
          pillBg: 'bg-amber-600/20 text-amber-300 border-amber-500/30',
          cardBorder: 'hover:border-amber-500/50 border-zinc-800'
        };
      case 'energy':
      default:
        return {
          glow: 'shadow-indigo-950/40 border-indigo-500/30 text-indigo-400',
          bgLight: 'bg-indigo-500/10',
          borderAccent: 'border-indigo-500',
          textAccent: 'text-indigo-400',
          buttonClass: 'bg-gradient-to-r from-indigo-600 to-violet-600 hover:from-indigo-500 hover:to-violet-500 text-white shadow-lg shadow-indigo-600/30',
          pillBg: 'bg-indigo-600/20 text-indigo-300 border-indigo-500/30',
          cardBorder: 'hover:border-indigo-500/50 border-zinc-800'
        };
    }
  };

  const themeClasses = getThemeClasses(activeGoal);

  return (
    <div className="min-h-screen bg-[#09090b] text-zinc-100 font-sans antialiased selection:bg-orange-500 selection:text-white">
      
      {/* HEADER / BRAND HERO BAR */}
      <header className="sticky top-0 z-40 backdrop-blur-md bg-[#09090b]/90 border-b border-zinc-800">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="relative group cursor-pointer">
              <div className="absolute -inset-1 rounded-xl bg-gradient-to-r from-emerald-500 via-amber-400 to-emerald-500 opacity-50 blur group-hover:opacity-90 transition duration-1000"></div>
              <div className="relative bg-zinc-950 px-3 py-1.5 rounded-xl border border-zinc-800 flex items-center gap-2.5">
                {/* Custom HX Logo SVG */}
                <HXLogo className="w-9 h-9 shrink-0" />
                <div className="flex flex-col leading-none">
                  <span className="font-extrabold text-sm sm:text-base tracking-wider text-emerald-400">
                    HEALTH<span className="text-amber-400">X</span>PRESS
                  </span>
                  <span className="text-[8px] font-bold tracking-[0.2em] text-zinc-500 mt-0.5 hidden sm:block">
                    EAT CLEAN. TRAIN HARD.
                  </span>
                </div>
                <span className="text-[10px] bg-emerald-500 text-zinc-950 font-black px-1.5 py-0.5 rounded-md ml-1">
                  30G PROTEIN
                </span>
              </div>
            </div>
          </div>

          {/* Quick Active Goal Toggle to showcase dynamic theme switching */}
          <div className="hidden md:flex items-center gap-2 bg-zinc-900/80 p-1 rounded-xl border border-zinc-800">
            <span className="text-xs font-semibold text-zinc-400 px-2">Goal:</span>
            {(Object.keys(GOAL_THEMES) as Array<keyof typeof GOAL_THEMES>).map((gId) => (
              <button
                key={gId}
                onClick={() => setActiveGoal(gId)}
                className={`text-xs px-2.5 py-1.5 rounded-lg font-medium transition-all ${
                  activeGoal === gId
                    ? `${GOAL_THEMES[gId].accentBg} text-zinc-950 shadow-md font-bold scale-105`
                    : 'text-zinc-400 hover:bg-zinc-800 hover:text-white'
                }`}
              >
                {GOAL_THEMES[gId].name.split(' & ')[0]}
              </button>
            ))}
          </div>

          <div className="flex items-center gap-3">
            {/* Streak Indicator */}
            <div 
              onClick={() => {
                const element = document.getElementById('gamification-hub');
                if (element) element.scrollIntoView({ behavior: 'smooth' });
                showToast("🔥 Hustler Loyalty Program: You're currently on a 2-Month Streak!");
              }}
              className="flex items-center gap-1.5 bg-zinc-900 border border-orange-500/40 hover:border-orange-400 text-orange-400 px-3 py-1.5 rounded-xl cursor-pointer text-xs font-bold transition-all hover:scale-105"
            >
              <Zap className="w-4 h-4 fill-orange-400 animate-bounce" />
              <span>STREAK: {streakCount}M</span>
            </div>

            {/* Take Quiz Button */}
            <button
              onClick={() => {
                setQuizStep(1);
                setShowQuiz(true);
              }}
              className="hidden sm:flex items-center gap-1 bg-zinc-800 hover:bg-zinc-700 text-zinc-200 hover:text-white px-3 py-1.5 rounded-xl text-xs font-medium border border-zinc-700 transition"
            >
              <Sparkles className="w-3.5 h-3.5 text-yellow-400" />
              <span>Retake Advisor Quiz</span>
            </button>
          </div>
        </div>
      </header>

      {/* GLOBAL TOAST NOTIFICATION BANNER */}
      {notification && (
        <div className="bg-zinc-900 border-b border-orange-500/50 text-zinc-200 py-3 px-4 shadow-2xl transition-all duration-500 animate-fadeIn relative z-30">
          <div className="max-w-7xl mx-auto flex items-center justify-between gap-3 text-xs sm:text-sm">
            <div className="flex items-center gap-2">
              <span className="flex h-2 w-2 relative">
                <span className="animate-ping absolute inline-flex h-full w-full rounded-full bg-orange-400 opacity-75"></span>
                <span className="relative inline-flex rounded-full h-2 w-2 bg-orange-500"></span>
              </span>
              <p className="font-medium text-zinc-300">
                <span className="text-orange-400 font-bold mr-1">SYSTEM ALERT:</span> {notification}
              </p>
            </div>
            <button 
              onClick={() => setNotification(null)}
              className="text-zinc-500 hover:text-zinc-300 font-bold px-1"
            >
              ✕
            </button>
          </div>
        </div>
      )}

      {/* INTEGRATION PROMPT (If on desktop, remind about switching themes) */}
      <div className="bg-gradient-to-r from-zinc-900 via-orange-950/20 to-zinc-900 border-b border-zinc-800 py-2.5 px-4 text-center text-xs text-zinc-400 hidden sm:block">
        💡 <strong className="text-zinc-300">Strategy Highlight:</strong> Watch the entire website branding, recommended recipes, and background styles change instantly when you switch goals below or through the Advisor Quiz!
      </div>

      <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8 space-y-16">

        {/* ONBOARDING QUIZ OVERLAY/MODAL */}
        {showQuiz && (
          <div className="fixed inset-0 z-50 bg-black/80 backdrop-blur-md flex items-center justify-center p-4">
            <div className="bg-zinc-950 rounded-3xl border-2 border-zinc-800 max-w-2xl w-full p-6 sm:p-8 relative overflow-hidden shadow-2xl shadow-orange-500/5">
              <div className="absolute top-0 right-0 p-4">
                <button
                  onClick={() => setShowQuiz(false)}
                  className="text-zinc-500 hover:text-zinc-300 text-sm font-semibold border border-zinc-800 px-3 py-1 rounded-lg"
                >
                  ✕ Close / Skip
                </button>
              </div>

              {/* Progress bar */}
              <div className="w-full bg-zinc-900 h-1.5 rounded-full mb-6 overflow-hidden">
                <div
                  className="bg-gradient-to-r from-orange-500 to-red-500 h-full transition-all duration-300"
                  style={{ width: `${(quizStep / 3) * 100}%` }}
                ></div>
              </div>

              <div className="text-center mb-6">
                <span className="text-orange-500 text-xs font-bold tracking-widest uppercase bg-orange-500/10 px-3 py-1 rounded-full border border-orange-500/20 inline-block mb-2">
                  THE "FUEL YOUR GOAL" ADVISOR
                </span>
                <h2 className="text-2xl sm:text-3xl font-extrabold text-white">
                  What Are You Fueling?
                </h2>
                <p className="text-zinc-400 text-xs sm:text-sm mt-1">
                  Configure your 30G Protein Bowl subscription in 30 seconds.
                </p>
              </div>

              {/* STEP 1: Daily Grind */}
              {quizStep === 1 && (
                <div className="space-y-4">
                  <label className="block text-sm font-semibold text-zinc-300 text-center mb-2">
                    Question 1: What describes your daily grind?
                  </label>
                  <div className="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    {[
                      { id: 'Coding/Desk', label: 'Desk Warrior / Coder', icon: '💻', desc: 'Focus, zero afternoon crashes, long desk hours' },
                      { id: 'Gym/Lifting', label: 'Gym Hustler / Builder', icon: '🏋️', desc: 'Heavy strength sessions, maximum protein recovery' },
                      { id: 'Running/Athletics', label: 'Endurance Runner / Cyclist', icon: '🏃', desc: 'High metabolic load, stamina requirements' },
                      { id: 'Multi-tasker', label: 'Busy Parent / Operator', icon: '⚡', desc: 'Rapid energy, convenient sustained health profile' }
                    ].map((opt) => (
                      <button
                        key={opt.id}
                        onClick={() => setQuizAnswers({ ...quizAnswers, dailyGrind: opt.id })}
                        className={`p-4 rounded-xl text-left border-2 transition-all flex gap-3 ${
                          quizAnswers.dailyGrind === opt.id
                            ? 'border-orange-500 bg-orange-950/20 shadow-lg'
                            : 'border-zinc-800 bg-zinc-900/50 hover:border-zinc-700'
                        }`}
                      >
                        <span className="text-2xl">{opt.icon}</span>
                        <div>
                          <p className="font-bold text-white text-sm">{opt.label}</p>
                          <p className="text-xs text-zinc-400 mt-0.5">{opt.desc}</p>
                        </div>
                      </button>
                    ))}
                  </div>
                </div>
              )}

              {/* STEP 2: Fitness Goal */}
              {quizStep === 2 && (
                <div className="space-y-4">
                  <label className="block text-sm font-semibold text-zinc-300 text-center mb-2">
                    Question 2: Select your primary nutritional objective:
                  </label>
                  <div className="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    {[
                      { id: 'muscle', label: 'Muscle Gain & Power', icon: '🔥', desc: '30G Protein + high complex carb density' },
                      { id: 'weight', label: 'Weight Loss & Shred', icon: '🥗', desc: 'Low-carb greens base, high fullness factor' },
                      { id: 'energy', label: 'Sustained Focus (Coding)', icon: '🧠', desc: 'Brain-fuel fats (Almonds, Seeds) + light grain' },
                      { id: 'endurance', label: 'Endurance & Pace', icon: '📈', desc: 'Electrolytes, rapid absorption sweet potato base' }
                    ].map((opt) => (
                      <button
                        key={opt.id}
                        onClick={() => setQuizAnswers({ ...quizAnswers, fitnessGoal: opt.id as keyof typeof GOAL_THEMES })}
                        className={`p-4 rounded-xl text-left border-2 transition-all flex gap-3 ${
                          quizAnswers.fitnessGoal === opt.id
                            ? 'border-orange-500 bg-orange-950/20'
                            : 'border-zinc-800 bg-zinc-900/50 hover:border-zinc-700'
                        }`}
                      >
                        <span className="text-2xl">{opt.icon}</span>
                        <div>
                          <p className="font-bold text-white text-sm">{opt.label}</p>
                          <p className="text-xs text-zinc-400 mt-0.5">{opt.desc}</p>
                        </div>
                      </button>
                    ))}
                  </div>
                </div>
              )}

              {/* STEP 3: Allergies */}
              {quizStep === 3 && (
                <div className="space-y-4">
                  <label className="block text-sm font-semibold text-zinc-300 text-center mb-2">
                    Question 3: Any dietary limits or allergen exclusions?
                  </label>
                  <div className="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    {[
                      { id: 'None', label: 'No Restrictions', icon: '✅', desc: 'I eat everything, feed me all nutrients' },
                      { id: 'Vegan', label: 'Plant-Based Only', icon: '🌱', desc: 'Exclude dairy toppings like Paneer' },
                      { id: 'Gluten-Free', label: 'Gluten-Free', icon: '🌾', desc: 'No wheat-derived ingredients' },
                      { id: 'Nut-Free', label: 'Nut-Free', icon: '🚫', desc: 'Do not recommend almonds or walnuts' }
                    ].map((opt) => (
                      <button
                        key={opt.id}
                        onClick={() => setQuizAnswers({ ...quizAnswers, allergies: opt.id })}
                        className={`p-4 rounded-xl text-left border-2 transition-all flex gap-3 ${
                          quizAnswers.allergies === opt.id
                            ? 'border-orange-500 bg-orange-950/20'
                            : 'border-zinc-800 bg-zinc-900/50 hover:border-zinc-700'
                        }`}
                      >
                        <span className="text-2xl">{opt.icon}</span>
                        <div>
                          <p className="font-bold text-white text-sm">{opt.label}</p>
                          <p className="text-xs text-zinc-400 mt-0.5">{opt.desc}</p>
                        </div>
                      </button>
                    ))}
                  </div>
                </div>
              )}

              {/* Bottom buttons */}
              <div className="mt-8 flex justify-between items-center gap-3">
                {quizStep > 1 ? (
                  <button
                    onClick={() => setQuizStep(prev => prev - 1)}
                    className="px-5 py-2.5 rounded-xl border border-zinc-800 text-zinc-400 hover:bg-zinc-900 text-sm font-medium"
                  >
                    Back
                  </button>
                ) : (
                  <div className="w-1"></div>
                )}

                {quizStep < 3 ? (
                  <button
                    onClick={() => setQuizStep(prev => prev + 1)}
                    className="bg-orange-500 hover:bg-orange-400 text-zinc-950 font-bold px-6 py-2.5 rounded-xl text-sm flex items-center gap-1.5 ml-auto"
                  >
                    <span>Next Question</span>
                    <ArrowRight className="w-4 h-4" />
                  </button>
                ) : (
                  <button
                    onClick={handleCompleteQuiz}
                    className="bg-gradient-to-r from-orange-500 to-red-500 hover:from-orange-400 hover:to-red-400 text-zinc-950 font-black px-8 py-3 rounded-xl text-sm flex items-center gap-2 shadow-lg shadow-orange-500/20 ml-auto"
                  >
                    <span>Generate My Fuel Plan</span>
                    <Sparkles className="w-4 h-4 text-zinc-950 fill-zinc-950" />
                  </button>
                )}
              </div>
            </div>
          </div>
        )}

        {/* PERSISTENT GOAL BAR & THEMATIC SUMMARY */}
        <section className={`rounded-3xl p-6 sm:p-8 bg-zinc-950 border-2 ${themeClasses.borderAccent} shadow-2xl ${themeClasses.glow} transition-all duration-700 relative overflow-hidden`}>
          <div className="absolute top-0 right-0 transform translate-x-12 -translate-y-12 w-96 h-96 bg-zinc-900/30 rounded-full blur-3xl pointer-events-none"></div>
          
          <div className="relative z-10 flex flex-col lg:flex-row items-start lg:items-center justify-between gap-6">
            <div className="space-y-3 max-w-2xl">
              <div className="flex flex-wrap items-center gap-2">
                <span className={`text-xs font-bold tracking-widest px-3 py-1 rounded-full uppercase border ${themeClasses.pillBg} animate-pulse`}>
                  {currentTheme.badge}
                </span>
                <span className="text-xs text-zinc-400 bg-zinc-900 px-3 py-1 rounded-full border border-zinc-800 flex items-center gap-1">
                  <CheckCircle2 className="w-3 h-3 text-emerald-500" />
                  Personalized Sourcing
                </span>
              </div>
              
              <h1 className="text-3xl sm:text-4xl lg:text-5xl font-black text-white tracking-tight leading-tight">
                Fueling: <span className={`bg-clip-text text-transparent bg-gradient-to-r ${currentTheme.gradientFromTo}`}>{currentTheme.name}</span>
              </h1>
              
              <p className="text-zinc-300 text-sm sm:text-base leading-relaxed">
                "{currentTheme.quote}"
              </p>

              <div className="flex flex-wrap gap-4 pt-1">
                <div className="bg-zinc-900/80 border border-zinc-800 rounded-xl px-4 py-2 text-center min-w-[90px]">
                  <p className="text-[10px] text-zinc-500 uppercase tracking-wider font-bold">Base Protein</p>
                  <p className="text-lg font-black text-white">30G</p>
                </div>
                <div className="bg-zinc-900/80 border border-zinc-800 rounded-xl px-4 py-2 text-center min-w-[90px]">
                  <p className="text-[10px] text-zinc-500 uppercase tracking-wider font-bold">Goal Target</p>
                  <p className={`text-lg font-black ${themeClasses.textAccent}`}>{activeGoal === 'muscle' ? 'Bulking' : activeGoal === 'weight' ? 'Shredding' : activeGoal === 'energy' ? 'Focus' : 'Stamina'}</p>
                </div>
                <div className="bg-zinc-900/80 border border-zinc-800 rounded-xl px-4 py-2 text-center min-w-[90px]">
                  <p className="text-[10px] text-zinc-500 uppercase tracking-wider font-bold">Sprout-Base</p>
                  <p className="text-lg font-black text-zinc-300">Peanut-Moong</p>
                </div>
              </div>
            </div>

            {/* Quick switcher & summary visual */}
            <div className="w-full lg:w-auto bg-zinc-900/60 rounded-2xl p-4 sm:p-6 border border-zinc-800/80 max-w-md">
              <h3 className="text-xs font-bold text-zinc-400 uppercase tracking-widest mb-3 flex items-center gap-2">
                <Sliders className="w-3.5 h-3.5 text-orange-500" />
                DASHBOARD THEME SELECTOR
              </h3>
              <p className="text-xs text-zinc-500 mb-3 leading-relaxed">
                Select your fitness objective below. The entire web interface & customizer will recalibrate for your goal immediately.
              </p>
              
              <div className="grid grid-cols-2 gap-2">
                {(Object.keys(GOAL_THEMES) as Array<keyof typeof GOAL_THEMES>).map((gId) => {
                  const item = GOAL_THEMES[gId];
                  const isActive = activeGoal === gId;
                  return (
                    <button
                      key={gId}
                      onClick={() => setActiveGoal(gId)}
                      className={`p-2.5 rounded-xl text-left border transition-all relative ${
                        isActive
                          ? `border-${item.colorClass}-500 bg-zinc-950 shadow-md`
                          : 'border-zinc-800 hover:border-zinc-700 bg-zinc-950/40'
                      }`}
                    >
                      <div className="flex items-center justify-between">
                        <p className={`text-xs font-bold ${isActive ? 'text-white' : 'text-zinc-400'}`}>
                          {item.name.split(' & ')[0]}
                        </p>
                        {isActive && (
                          <span className={`h-1.5 w-1.5 rounded-full ${item.accentBg} animate-ping`}></span>
                        )}
                      </div>
                      <p className="text-[9px] text-zinc-500 mt-0.5 truncate">{item.tagline.split(' ')[0]} focused</p>
                    </button>
                  );
                })}
              </div>

              <div className="mt-4 flex justify-between items-center bg-zinc-950 p-2.5 rounded-xl border border-zinc-800">
                <div className="flex items-center gap-2">
                  <div className="w-2.5 h-2.5 rounded-full bg-emerald-500 animate-pulse"></div>
                  <span className="text-[10px] text-zinc-400 font-semibold uppercase tracking-wider">Subscribed via HealthXpress</span>
                </div>
                <button 
                  onClick={() => {
                    setQuizStep(1);
                    setShowQuiz(true);
                  }}
                  className="text-[10px] text-orange-400 hover:text-orange-300 font-bold underline cursor-pointer"
                >
                  Restart Quiz ↺
                </button>
              </div>
            </div>
          </div>
        </section>


        {/* PILLAR 1: MODULAR BOWL CUSTOMIZER WITH "MACRO LOCK" */}
        <section id="bowl-customizer" className="scroll-mt-20">
          <div className="mb-6 flex flex-col md:flex-row md:items-end justify-between gap-4">
            <div>
              <div className="flex items-center gap-2 mb-1">
                <ChefHat className={`w-5 h-5 ${themeClasses.textAccent}`} />
                <span className="text-xs font-bold text-orange-500 tracking-wider uppercase">PILLAR 1: HYPER-PERSONALIZATION</span>
              </div>
              <h2 className="text-2xl sm:text-3xl font-extrabold text-white">
                Modular Bowl Customizer with <span className="text-orange-500">"Macro Lock"</span>
              </h2>
              <p className="text-zinc-400 text-xs sm:text-sm mt-1 max-w-2xl">
                Build your ultimate subscription box. Toggle **Macro Lock** to guarantee exactly **30G Protein** regardless of your base or vegetable toppings. The AI engine scales portions automatically.
              </p>
            </div>

            {/* Macro Lock Toggle Box */}
            <div className="bg-zinc-950 border border-zinc-800 p-3 rounded-2xl flex items-center justify-between gap-4 min-w-[280px]">
              <div className="flex items-center gap-2.5">
                <div className={`p-1.5 rounded-lg ${macroLock ? 'bg-orange-500/20 text-orange-400' : 'bg-zinc-800 text-zinc-500'}`}>
                  {macroLock ? <Lock className="w-4 h-4 animate-bounce" /> : <Unlock className="w-4 h-4" />}
                </div>
                <div>
                  <p className="text-xs font-bold text-white flex items-center gap-1">
                    Macro Lock: 30G Protein
                  </p>
                  <p className="text-[10px] text-zinc-500">
                    {macroLock ? 'Lock active (Ratios adjusted)' : 'Off (Raw ingredient values)'}
                  </p>
                </div>
              </div>

              {/* Toggle switch */}
              <button
                onClick={() => {
                  setMacroLock(!macroLock);
                  showToast(macroLock ? '🔓 Macro Lock disabled. Customizer is now calculating raw values.' : '🔒 Macro Lock ACTIVE. Customizer locked to exactly 30G Protein!');
                }}
                className={`w-12 h-6 rounded-full p-1 transition-colors duration-300 ${
                  macroLock ? 'bg-orange-500' : 'bg-zinc-800'
                }`}
              >
                <div
                  className={`bg-zinc-950 w-4 h-4 rounded-full shadow-md transform transition-transform duration-300 ${
                    macroLock ? 'translate-x-6' : 'translate-x-0'
                  }`}
                ></div>
              </button>
            </div>
          </div>

          <div className="grid grid-cols-1 lg:grid-cols-12 gap-8">
            
            {/* INGREDIENT SELECTION PANELS (8-columns) */}
            <div className="lg:col-span-8 space-y-6">
              
              {/* Core mix banner */}
              <div className="bg-zinc-950 border border-zinc-800/80 rounded-2xl p-4 flex flex-col sm:flex-row sm:items-center justify-between gap-3 relative overflow-hidden">
                <div className="absolute top-0 left-0 bg-orange-500 w-1.5 h-full"></div>
                <div className="space-y-1 pl-2">
                  <div className="flex items-center gap-1.5">
                    <span className="text-xs font-black text-emerald-400 tracking-wider bg-emerald-500/10 px-2 py-0.5 rounded border border-emerald-500/20">HEALTHXPRESS CORE MIX</span>
                    <span className="text-xs text-zinc-400">(Always included in your base)</span>
                  </div>
                  <h4 className="text-sm font-bold text-white">{CORE_PROTEIN_MIX.name}</h4>
                  <p className="text-xs text-zinc-400 max-w-xl">{CORE_PROTEIN_MIX.desc}</p>
                </div>
                <div className="text-right shrink-0 bg-zinc-900 px-3 py-1.5 rounded-xl border border-zinc-800">
                  <span className="text-xs font-extrabold text-white block">CORE MACROS</span>
                  <span className="text-xs font-semibold text-zinc-400">15g Pro | 22g Carb | 160 Kcal</span>
                </div>
              </div>

              {/* STEP 1: SELECT BASE (Single Choice) */}
              <div className="bg-zinc-950 rounded-2xl p-4 sm:p-5 border border-zinc-800">
                <div className="flex items-center justify-between mb-3 border-b border-zinc-900 pb-2">
                  <div className="flex items-center gap-2">
                    <span className="bg-zinc-900 text-zinc-400 text-xs font-black w-6 h-6 rounded-full flex items-center justify-center border border-zinc-800">1</span>
                    <h3 className="text-sm font-bold text-white uppercase tracking-wider">Choose Base Grain or Veggie</h3>
                  </div>
                  {selectedBase && (
                    <span className="text-xs font-semibold text-emerald-400 flex items-center gap-1">
                      <Check className="w-3.5 h-3.5" /> Selected
                    </span>
                  )}
                </div>
                
                <div className="grid grid-cols-1 sm:grid-cols-3 gap-3">
                  {INGREDIENTS.filter(i => i.category === 'base').map((base) => {
                    const isSelected = selectedBase === base.id;
                    const isRecommended = currentTheme.recommendedBase === base.id;
                    return (
                      <button
                        key={base.id}
                        onClick={() => setSelectedBase(base.id)}
                        className={`p-3 rounded-xl border text-left transition-all relative flex flex-col justify-between min-h-[110px] ${
                          isSelected
                            ? `border-orange-500 bg-orange-500/5`
                            : `border-zinc-800 hover:border-zinc-700 bg-zinc-900/25`
                        }`}
                      >
                        <div>
                          <div className="flex items-center justify-between">
                            <span className="text-xl">{base.icon}</span>
                            {isRecommended && (
                              <span className={`text-[8px] font-bold px-1.5 py-0.5 rounded-full border ${themeClasses.pillBg}`}>
                                REC
                              </span>
                            )}
                          </div>
                          <p className="text-xs font-bold text-white mt-2 truncate">{base.name}</p>
                          <p className="text-[10px] text-zinc-500 line-clamp-2 mt-0.5">{base.desc}</p>
                        </div>
                        <div className="mt-2 text-[10px] text-zinc-400 font-semibold border-t border-zinc-900 pt-1.5 flex justify-between">
                          <span>+{base.protein}g Protein</span>
                          <span className="text-zinc-500">{base.calories} kcal</span>
                        </div>
                      </button>
                    );
                  })}
                </div>
              </div>

              {/* STEP 2: SELECT TOPPINGS (Multiple Choice) */}
              <div className="bg-zinc-950 rounded-2xl p-4 sm:p-5 border border-zinc-800">
                <div className="flex items-center justify-between mb-3 border-b border-zinc-900 pb-2">
                  <div className="flex items-center gap-2">
                    <span className="bg-zinc-900 text-zinc-400 text-xs font-black w-6 h-6 rounded-full flex items-center justify-center border border-zinc-800">2</span>
                    <h3 className="text-sm font-bold text-white uppercase tracking-wider">Select Premium Toppings (Multiple)</h3>
                  </div>
                  <span className="text-xs text-zinc-400 font-semibold">
                    {selectedToppings.length} selected
                  </span>
                </div>

                <div className="grid grid-cols-2 sm:grid-cols-4 gap-3">
                  {INGREDIENTS.filter(i => i.category === 'topping').map((topping) => {
                    const isSelected = selectedToppings.includes(topping.id);
                    const isRecommended = currentTheme.recommendedToppings.includes(topping.id);
                    return (
                      <button
                        key={topping.id}
                        onClick={() => handleToggleTopping(topping.id)}
                        className={`p-3 rounded-xl border text-left transition-all flex flex-col justify-between min-h-[110px] relative ${
                          isSelected
                            ? `border-orange-500 bg-orange-500/5`
                            : 'border-zinc-800 hover:border-zinc-700 bg-zinc-900/25'
                        }`}
                      >
                        {isSelected && (
                          <div className="absolute top-2 right-2 bg-orange-500 rounded-full p-0.5">
                            <Check className="w-3 h-3 text-zinc-950 stroke-[3]" />
                          </div>
                        )}
                        <div>
                          <div className="flex items-center justify-between">
                            <span className="text-xl">{topping.icon}</span>
                            {isRecommended && !isSelected && (
                              <span className={`text-[8px] font-bold px-1 py-0.2 rounded-full border ${themeClasses.pillBg}`}>
                                REC
                              </span>
                            )}
                          </div>
                          <p className="text-xs font-bold text-white mt-2 truncate">{topping.name.replace('Organic ', '').replace('Fresh ', '')}</p>
                          <p className="text-[10px] text-zinc-500 line-clamp-1 mt-0.5">{topping.desc}</p>
                        </div>
                        <div className="mt-2 text-[10px] text-zinc-400 font-semibold border-t border-zinc-900 pt-1.5 flex justify-between">
                          <span>+{topping.protein}g Protein</span>
                          <span className="text-zinc-500">{topping.calories} kcal</span>
                        </div>
                      </button>
                    );
                  })}
                </div>
              </div>

              {/* STEP 3: CHOOSE HUSTLER SAUCE (Single Choice) */}
              <div className="bg-zinc-950 rounded-2xl p-4 sm:p-5 border border-zinc-800">
                <div className="flex items-center justify-between mb-3 border-b border-zinc-900 pb-2">
                  <div className="flex items-center gap-2">
                    <span className="bg-zinc-900 text-zinc-400 text-xs font-black w-6 h-6 rounded-full flex items-center justify-center border border-zinc-800">3</span>
                    <h3 className="text-sm font-bold text-white uppercase tracking-wider">Choose Your Hustler Sauce</h3>
                  </div>
                  {selectedSauce && (
                    <span className="text-xs font-semibold text-emerald-400 flex items-center gap-1">
                      <Check className="w-3.5 h-3.5" /> Selected
                    </span>
                  )}
                </div>

                <div className="grid grid-cols-1 sm:grid-cols-5 gap-2.5">
                  {INGREDIENTS.filter(i => i.category === 'sauce').map((sauce) => {
                    const isSelected = selectedSauce === sauce.id;
                    const isRecommended = currentTheme.recommendedSauce === sauce.id;
                    return (
                      <button
                        key={sauce.id}
                        onClick={() => setSelectedSauce(sauce.id)}
                        className={`p-2.5 rounded-xl border text-left transition-all flex flex-col justify-between min-h-[95px] relative ${
                          isSelected
                            ? `border-orange-500 bg-orange-500/5`
                            : 'border-zinc-800 hover:border-zinc-700 bg-zinc-900/25'
                        }`}
                      >
                        <div>
                          <div className="flex items-center justify-between">
                            <span className="text-lg">{sauce.icon}</span>
                            {isRecommended && (
                              <span className={`text-[8px] font-bold px-1 py-0.2 rounded-full border ${themeClasses.pillBg}`}>
                                REC
                              </span>
                            )}
                          </div>
                          <p className="text-[11px] font-bold text-white mt-1.5 truncate">{sauce.name.split(' Sauce')[0].replace('Energy ', '')}</p>
                        </div>
                        <div className="text-[9px] text-zinc-500 mt-1 border-t border-zinc-900 pt-1 flex justify-between">
                          <span>+{sauce.protein}g Pro</span>
                          <span>{sauce.calories} kcal</span>
                        </div>
                      </button>
                    );
                  })}
                </div>
              </div>

            </div>

            {/* LIVE BOWL MACRO GRAPH & ORDER CARD (4-columns) */}
            <div className="lg:col-span-4 space-y-6">
              
              <div className="bg-zinc-950 rounded-2xl p-5 border border-zinc-800 relative overflow-hidden shadow-xl sticky top-24">
                
                {/* Visual Bowl Render Preview */}
                <div className="relative h-44 rounded-xl overflow-hidden mb-6 bg-zinc-900 border border-zinc-800 flex items-center justify-center">
                  <img
                    src="public/images/protein-bowl.jpg"
                    alt="Custom health bowl"
                    className="absolute inset-0 w-full h-full object-cover opacity-80"
                  />
                  <div className="absolute inset-0 bg-gradient-to-t from-zinc-950 via-zinc-950/20 to-transparent"></div>
                  
                  {/* Floating goal tag */}
                  <span className={`absolute top-3 left-3 text-[10px] font-extrabold px-2.5 py-1 rounded-md uppercase tracking-wider border ${themeClasses.pillBg}`}>
                    {GOAL_THEMES[activeGoal].badge} Recommended
                  </span>

                  <div className="absolute bottom-3 left-3 right-3 text-left">
                    <p className="text-xs text-orange-400 font-bold uppercase tracking-widest">SUB-BOX PREVIEW</p>
                    <h3 className="text-sm font-extrabold text-white truncate">
                      HealthXpress My-{GOAL_THEMES[activeGoal].name.split(' ')[0]} Bowl
                    </h3>
                  </div>
                </div>

                {/* Macro lock indicator banner */}
                {macroLock && (
                  <div className="bg-emerald-950/20 border border-emerald-500/40 rounded-xl p-3 mb-4 text-center">
                    <div className="flex items-center justify-center gap-1.5 text-emerald-400 font-extrabold text-xs">
                      <Lock className="w-3.5 h-3.5" />
                      <span>30G PROTEIN LOCK ACTIVE</span>
                    </div>
                    <p className="text-[10px] text-zinc-400 mt-0.5 leading-tight">
                      Adjusting base and toppings to maintain a 100% strict 30g protein macro profile.
                    </p>
                  </div>
                )}

                {/* Macro breakdown sliders */}
                <h4 className="text-xs font-bold text-zinc-400 uppercase tracking-widest mb-3 flex items-center gap-1.5">
                  <Activity className="w-4 h-4 text-orange-500" />
                  Visual Macro Lock Panel
                </h4>

                <div className="space-y-4 mb-6">
                  {/* PROTEIN BAR */}
                  <div>
                    <div className="flex justify-between items-end text-xs mb-1">
                      <span className="font-bold text-zinc-300 flex items-center gap-1">
                        Protein
                        {macroLock && <Lock className="w-2.5 h-2.5 text-emerald-400" />}
                      </span>
                      <span className={`font-black ${macroLock ? 'text-emerald-400 text-sm' : 'text-white'}`}>
                        {macros.protein}g / 30g
                      </span>
                    </div>
                    <div className="w-full bg-zinc-900 h-2.5 rounded-full overflow-hidden">
                      <div
                        className={`h-full transition-all duration-500 ${
                          macroLock ? 'bg-emerald-500 shadow-[0_0_8px_rgba(16,185,129,0.5)]' : 'bg-orange-500'
                        }`}
                        style={{ width: `${(macros.protein / 30) * 100}%` }}
                      ></div>
                    </div>
                  </div>

                  {/* CALORIES BAR */}
                  <div>
                    <div className="flex justify-between items-end text-xs mb-1">
                      <span className="font-bold text-zinc-300">Calories</span>
                      <span className="font-black text-white">{macros.calories} Kcal</span>
                    </div>
                    <div className="w-full bg-zinc-950 h-2 rounded-full overflow-hidden">
                      <div
                        className="bg-yellow-500 h-full transition-all duration-500"
                        style={{ width: `${Math.min((macros.calories / 700) * 100, 100)}%` }}
                      ></div>
                    </div>
                  </div>

                  {/* CARBS & FAT DOUBLE SPLIT */}
                  <div className="grid grid-cols-2 gap-3 pt-1">
                    <div className="bg-zinc-900/50 p-2 rounded-xl border border-zinc-800">
                      <div className="flex justify-between items-center text-[10px] mb-1">
                        <span className="text-zinc-400 font-bold">Carbs</span>
                        <span className="text-white font-extrabold">{macros.carbs}g</span>
                      </div>
                      <div className="w-full bg-zinc-950 h-1.5 rounded-full overflow-hidden">
                        <div className="bg-indigo-400 h-full" style={{ width: `${Math.min((macros.carbs / 80) * 100, 100)}%` }}></div>
                      </div>
                    </div>

                    <div className="bg-zinc-900/50 p-2 rounded-xl border border-zinc-800">
                      <div className="flex justify-between items-center text-[10px] mb-1">
                        <span className="text-zinc-400 font-bold">Fats</span>
                        <span className="text-white font-extrabold">{macros.fat}g</span>
                      </div>
                      <div className="w-full bg-zinc-950 h-1.5 rounded-full overflow-hidden">
                        <div className="bg-red-400 h-full" style={{ width: `${Math.min((macros.fat / 40) * 100, 100)}%` }}></div>
                      </div>
                    </div>
                  </div>
                </div>

                {/* Adjustment notification */}
                {macroLock && macros.isAutoAdjusted && (
                  <div className="text-[10px] bg-emerald-500/10 border border-emerald-500/20 rounded-xl p-2.5 mb-5 text-emerald-300 flex items-start gap-1.5">
                    <Sparkles className="w-3.5 h-3.5 text-emerald-400 shrink-0 mt-0.5" />
                    <span>{macros.adjustmentMsg}</span>
                  </div>
                )}

                {/* Ingredient recap list */}
                <div className="border-t border-zinc-900 pt-4 mb-5">
                  <span className="text-[10px] text-zinc-500 font-bold uppercase tracking-wider block mb-2">Ingredients Configured</span>
                  <div className="space-y-1 max-h-36 overflow-y-auto pr-1">
                    <div className="flex justify-between text-xs text-zinc-300">
                      <span className="truncate">🍲 Sprout Core Protein Blend</span>
                      <span className="font-semibold shrink-0">15g Pro</span>
                    </div>
                    {INGREDIENTS.find(i => i.id === selectedBase) && (
                      <div className="flex justify-between text-xs text-zinc-400">
                        <span className="truncate">🌾 Base: {INGREDIENTS.find(i => i.id === selectedBase)?.name}</span>
                        <span className="shrink-0">+{INGREDIENTS.find(i => i.id === selectedBase)?.protein}g Pro</span>
                      </div>
                    )}
                    {selectedToppings.map(tId => {
                      const topping = INGREDIENTS.find(i => i.id === tId);
                      return (
                        <div key={tId} className="flex justify-between text-xs text-zinc-400">
                          <span className="truncate">➕ Topping: {topping?.name}</span>
                          <span className="shrink-0">+{topping?.protein}g Pro</span>
                        </div>
                      );
                    })}
                    {INGREDIENTS.find(i => i.id === selectedSauce) && (
                      <div className="flex justify-between text-xs text-zinc-400">
                        <span className="truncate">🌶️ Sauce: {INGREDIENTS.find(i => i.id === selectedSauce)?.name}</span>
                        <span className="shrink-0">+{INGREDIENTS.find(i => i.id === selectedSauce)?.protein}g Pro</span>
                      </div>
                    )}
                  </div>
                </div>

                {/* Subscription Action Button */}
                <button
                  onClick={() => {
                    showToast('🛍️ Custom Subscription Box saved! Recalibrating monthly schedules.');
                    const element = document.getElementById('pause-calendar');
                    if (element) element.scrollIntoView({ behavior: 'smooth' });
                  }}
                  className={`w-full py-3 rounded-xl font-black text-xs sm:text-sm tracking-wider uppercase transition-all duration-300 transform active:scale-95 ${themeClasses.buttonClass}`}
                >
                  Save Custom Box Selection
                </button>
                <p className="text-[10px] text-zinc-500 text-center mt-2">
                  No lock-in contracts. Cancel or pause with 1 click in the calendar.
                </p>
              </div>

            </div>
          </div>
        </section>


        {/* PILLAR 2: COMMUNITY GAMIFICATION (Streak, Leaderboard, Challenge) */}
        <section id="gamification-hub" className="scroll-mt-20 border-t border-zinc-900 pt-16">
          <div className="mb-8">
            <div className="flex items-center gap-2 mb-1">
              <Trophy className="w-5 h-5 text-orange-500" />
              <span className="text-xs font-bold text-orange-500 tracking-wider uppercase">PILLAR 2: COMMUNITY GAMIFICATION</span>
            </div>
            <h2 className="text-2xl sm:text-3xl font-extrabold text-white">
              Hustlers Arena: Streaks & Leaderboards
            </h2>
            <p className="text-zinc-400 text-xs sm:text-sm mt-1 max-w-2xl">
              Consistent clean eating shouldn’t be boring. Track your consecutive subscription months to unlock high-end merch, and compete on the anonymous protein-fueled board!
            </p>
          </div>

          <div className="grid grid-cols-1 lg:grid-cols-12 gap-8">
            
            {/* HUSTLE STREAK TRACKER (7-columns) */}
            <div className="lg:col-span-7 bg-zinc-950 border border-zinc-800 rounded-3xl p-6 relative overflow-hidden flex flex-col justify-between">
              <div className="absolute top-0 right-0 p-4">
                <span className="text-[10px] font-black tracking-widest text-orange-400 bg-orange-500/10 border border-orange-500/20 px-2.5 py-1 rounded-full uppercase">
                  Streak-Rewards Active
                </span>
              </div>

              <div>
                <h3 className="text-lg font-bold text-white flex items-center gap-2 mb-1">
                  The "Hustle Streak" Loyalty Program
                </h3>
                <p className="text-xs text-zinc-400 mb-6 max-w-lg">
                  Consistency, not wallets, earns the highest rank here. Unlock non-monetary, limited-run brand gear by keeping your subscription active consecutively.
                </p>

                {/* 12-Month Interactive Timeline */}
                <div className="mb-8 overflow-x-auto pb-4">
                  <div className="min-w-[600px] px-2">
                    <div className="relative">
                      {/* Timeline connecting line */}
                      <div className="absolute top-1/2 left-0 right-0 h-1 bg-zinc-900 -translate-y-1/2 z-0"></div>
                      <div 
                        className="absolute top-1/2 left-0 h-1 bg-orange-500 -translate-y-1/2 z-0 transition-all duration-1000"
                        style={{ width: `${(streakCount / 12) * 100}%` }}
                      ></div>

                      <div className="flex justify-between relative z-10">
                        {[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12].map((m) => {
                          const isCompleted = m <= streakCount;
                          const isCurrent = m === streakCount;
                          const hasReward = m === 3 || m === 6 || m === 12;

                          return (
                            <div key={m} className="flex flex-col items-center">
                              <div
                                className={`w-8 h-8 rounded-full flex items-center justify-center font-bold text-xs border-2 transition-all duration-500 ${
                                  isCompleted
                                    ? 'bg-orange-500 border-orange-400 text-zinc-950 font-black'
                                    : isCurrent
                                    ? 'bg-zinc-900 border-orange-500 text-orange-400 animate-pulse'
                                    : 'bg-zinc-950 border-zinc-800 text-zinc-600'
                                }`}
                              >
                                {isCompleted ? '✓' : `${m}`}
                              </div>
                              <span className="text-[9px] font-semibold text-zinc-500 mt-2">M{m}</span>
                              {hasReward && (
                                <span className={`text-[9px] font-black tracking-tighter mt-1 px-1 py-0.2 rounded border ${
                                  isCompleted ? 'bg-orange-500/20 text-orange-300 border-orange-500/30' : 'bg-zinc-900 text-zinc-500 border-zinc-800'
                                }`}>
                                  {m === 3 ? 'FLASK 🧪' : m === 6 ? 'OFF-MENU 🌶️' : 'OWN-BOWL 👑'}
                                </span>
                              )}
                            </div>
                          );
                        })}
                      </div>
                    </div>
                  </div>
                </div>

                {/* Milestone Detail Cards */}
                <div className="grid grid-cols-1 sm:grid-cols-3 gap-3 mb-6">
                  {/* Streak 3 */}
                  <div className={`p-3.5 rounded-2xl border ${streakCount >= 3 ? 'border-orange-500/30 bg-orange-950/10' : 'border-zinc-900 bg-zinc-900/20'} transition-all`}>
                    <div className="flex items-center justify-between mb-1.5">
                      <span className="text-xs font-black text-zinc-300">3 Month Streak</span>
                      {streakCount >= 3 ? (
                        <span className="text-[9px] font-bold text-emerald-400 bg-emerald-500/10 border border-emerald-500/20 px-2 py-0.5 rounded-full">UNLOCKED</span>
                      ) : (
                        <span className="text-[9px] text-zinc-500">Locked</span>
                      )}
                    </div>
                    <div className="flex items-center gap-2">
                      <div className="text-xl">🧪</div>
                      <div>
                        <h4 className="text-xs font-bold text-white">The Hustler Flask</h4>
                        <p className="text-[10px] text-zinc-400 mt-0.5">Double-walled matte black insulated container.</p>
                      </div>
                    </div>
                  </div>

                  {/* Streak 6 */}
                  <div className={`p-3.5 rounded-2xl border ${streakCount >= 6 ? 'border-orange-500/30 bg-orange-950/10' : 'border-zinc-900 bg-zinc-900/20'} transition-all`}>
                    <div className="flex items-center justify-between mb-1.5">
                      <span className="text-xs font-black text-zinc-300">6 Month Streak</span>
                      {streakCount >= 6 ? (
                        <span className="text-[9px] font-bold text-emerald-400 bg-emerald-500/10 border border-emerald-500/20 px-2 py-0.5 rounded-full">UNLOCKED</span>
                      ) : (
                        <span className="text-[9px] text-zinc-500">Locked</span>
                      )}
                    </div>
                    <div className="flex items-center gap-2">
                      <div className="text-xl">🌶️</div>
                      <div>
                        <h4 className="text-xs font-bold text-white">Off-Menu Bowl</h4>
                        <p className="text-[10px] text-zinc-400 mt-0.5">Unlock secret recipes (Ghost Chili Sprouts blend).</p>
                      </div>
                    </div>
                  </div>

                  {/* Streak 12 */}
                  <div className={`p-3.5 rounded-2xl border ${streakCount >= 12 ? 'border-orange-500/30 bg-orange-950/10' : 'border-zinc-900 bg-zinc-900/20'} transition-all`}>
                    <div className="flex items-center justify-between mb-1.5">
                      <span className="text-xs font-black text-zinc-300">12 Month Streak</span>
                      {streakCount >= 12 ? (
                        <span className="text-[9px] font-bold text-emerald-400 bg-emerald-500/10 border border-emerald-500/20 px-2 py-0.5 rounded-full">UNLOCKED</span>
                      ) : (
                        <span className="text-[9px] text-zinc-500">Locked</span>
                      )}
                    </div>
                    <div className="flex items-center gap-2">
                      <div className="text-xl">👑</div>
                      <div>
                        <h4 className="text-xs font-bold text-white">Name Your Bowl</h4>
                        <p className="text-[10px] text-zinc-400 mt-0.5">Submit & name a monthly bowl in the national menu.</p>
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              {/* Simulation triggers for testing */}
              <div className="border-t border-zinc-900 pt-4 flex flex-col sm:flex-row sm:items-center justify-between gap-4">
                <div className="flex items-center gap-2">
                  <span className="flex h-2.5 w-2.5 relative">
                    <span className="animate-ping absolute inline-flex h-full w-full rounded-full bg-orange-400 opacity-75"></span>
                    <span className="relative inline-flex rounded-full h-2.5 w-2.5 bg-orange-500"></span>
                  </span>
                  <div>
                    <p className="text-xs font-bold text-zinc-300">Simulate Streak Progress:</p>
                    <p className="text-[10px] text-zinc-500">Click to receive this month's custom bowl & update stats.</p>
                  </div>
                </div>

                <div className="flex gap-2 shrink-0">
                  <button
                    onClick={triggerDeliverySimulation}
                    className={`px-4 py-2 rounded-xl text-xs font-bold transition-all flex items-center gap-1.5 ${
                      subscriptionDelivered 
                        ? 'bg-zinc-900 text-zinc-500 border border-zinc-800 cursor-not-allowed'
                        : 'bg-orange-500 hover:bg-orange-400 text-zinc-950'
                    }`}
                  >
                    <MousePointerClick className="w-3.5 h-3.5" />
                    <span>Deliver This Month's Bowl</span>
                  </button>
                  {streakCount > 2 && (
                    <button
                      onClick={() => {
                        setStreakCount(2);
                        setSubscriptionDelivered(false);
                        showToast('🔄 Streak simulator reset to 2 months!');
                      }}
                      className="px-2.5 py-1.5 rounded-xl text-zinc-500 hover:text-zinc-300 border border-zinc-800 text-xs font-medium"
                    >
                      Reset Streak
                    </button>
                  )}
                </div>
              </div>
            </div>

            {/* LEADERBOARD & PROGRESS PANEL (5-columns) */}
            <div className="lg:col-span-5 bg-zinc-950 border border-zinc-800 rounded-3xl p-6 flex flex-col justify-between">
              <div>
                <div className="flex items-center justify-between mb-4 pb-2 border-b border-zinc-900">
                  <div>
                    <h3 className="text-sm font-extrabold text-white uppercase tracking-wider">
                      Hustlers Leaderboard
                    </h3>
                    <p className="text-[10px] text-zinc-500">
                      Ranks users by total cumulative protein fueled.
                    </p>
                  </div>
                  <div className="text-right">
                    <span className="text-[10px] font-bold text-emerald-400 bg-emerald-500/10 border border-emerald-500/20 px-2 py-0.5 rounded">
                      TOP 10% GETS 15% OFF
                    </span>
                  </div>
                </div>

                {/* Challenge Card */}
                <div className="bg-zinc-900/70 border border-zinc-800 rounded-2xl p-3 mb-4">
                  <div className="flex items-center justify-between text-xs mb-1.5">
                    <span className="font-bold text-zinc-200">The 30-Day Lean Hustle Challenge</span>
                    <span className="text-orange-400 font-extrabold">{challengeProgress}% Complete</span>
                  </div>
                  <div className="w-full bg-zinc-950 h-2 rounded-full overflow-hidden mb-2">
                    <div className="bg-orange-500 h-full transition-all duration-700" style={{ width: `${challengeProgress}%` }}></div>
                  </div>
                  <p className="text-[10px] text-zinc-400 leading-snug">
                    Maintain your daily 30G Protein target to earn the <strong>"Consistent Fueler"</strong> badge and a discount code.
                  </p>
                </div>

                {/* Leaderboard Table */}
                <div className="space-y-1.5 max-h-56 overflow-y-auto pr-1">
                  {leaderboard.map((item) => (
                    <div
                      key={item.name}
                      className={`flex items-center justify-between p-2 rounded-xl border text-xs transition-all ${
                        item.isUser
                          ? 'border-orange-500/50 bg-orange-500/10 font-bold shadow-md shadow-orange-500/5'
                          : 'border-zinc-900 bg-zinc-900/30'
                      }`}
                    >
                      <div className="flex items-center gap-2.5">
                        <span className={`w-4 h-4 rounded-full flex items-center justify-center text-[10px] font-black ${
                          item.rank === 1 ? 'bg-yellow-500 text-zinc-950' : item.rank === 2 ? 'bg-zinc-300 text-zinc-950' : item.rank === 3 ? 'bg-amber-600 text-zinc-950' : 'bg-zinc-900 text-zinc-400'
                        }`}>
                          {item.rank}
                        </span>
                        <div>
                          <p className="text-zinc-200 font-bold">{item.name}</p>
                          <p className="text-[9px] text-zinc-500">{item.goal}</p>
                        </div>
                      </div>
                      
                      <div className="text-right">
                        <p className="text-zinc-200 font-extrabold">{item.totalProtein}g Protein</p>
                        <p className="text-[9px] text-zinc-500">{item.hoursLogged} Hustle Hours</p>
                      </div>
                    </div>
                  ))}
                </div>
              </div>

              {/* Action Board Logging Button */}
              <div className="border-t border-zinc-900 pt-4 mt-4">
                <button
                  onClick={handleLogFuel}
                  className={`w-full py-2.5 rounded-xl font-bold text-xs flex items-center justify-center gap-1.5 transition-all active:scale-95 ${
                    userLoggedToday 
                      ? 'bg-zinc-900 text-zinc-500 border border-zinc-800 cursor-not-allowed'
                      : 'bg-zinc-800 hover:bg-zinc-700 text-zinc-200 hover:text-white border border-zinc-700'
                  }`}
                >
                  <Award className="w-4 h-4 text-orange-400" />
                  <span>{userLoggedToday ? "Today's Gains Logged!" : "Log Today's Protein Bowl (+30g Protein)"}</span>
                </button>
              </div>

            </div>
          </div>
        </section>


        {/* PILLAR 3: TRANSPARENT UTILITY - SEED TO SPOON HUB */}
        <section id="transparency-hub" className="scroll-mt-20 border-t border-zinc-900 pt-16">
          <div className="mb-8">
            <div className="flex items-center gap-2 mb-1">
              <Leaf className="w-5 h-5 text-emerald-500" />
              <span className="text-xs font-bold text-emerald-500 tracking-wider uppercase">PILLAR 3: TRANSPARENT UTILITY</span>
            </div>
            <h2 className="text-2xl sm:text-3xl font-extrabold text-white">
              "Seed to Spoon" Sourcing & Transparency Hub
            </h2>
            <p className="text-zinc-400 text-xs sm:text-sm mt-1 max-w-2xl">
              We do not hide behind vague organic labels. View exact agricultural sourcing, cooperatively backed farms, and our daily packing quality control live feeds.
            </p>
          </div>

          <div className="grid grid-cols-1 lg:grid-cols-12 gap-8">
            
            {/* LIVESTREAM / HIGHLIGHT BOX (7-columns) */}
            <div className="lg:col-span-7 bg-zinc-950 border border-zinc-800 rounded-3xl p-5 relative overflow-hidden flex flex-col justify-between">
              
              <div>
                {/* Header of Stream */}
                <div className="flex items-center justify-between mb-4 pb-2 border-b border-zinc-900">
                  <div className="flex items-center gap-2">
                    <span className="flex h-2.5 w-2.5 relative">
                      <span className="animate-ping absolute inline-flex h-full w-full rounded-full bg-red-500 opacity-75"></span>
                      <span className="relative inline-flex rounded-full h-2.5 w-2.5 bg-red-600"></span>
                    </span>
                    <span className="text-xs font-bold text-white uppercase tracking-wider">
                      {activeVideo === 'qc' ? 'LIVE FEED: Quality Control & Packing Line 3' : activeVideo === 'farm' ? 'HIGHLIGHTS: Organic Peanut & Moong Sourcing' : 'LIVE FEED: Sprouting Kitchen Line B'}
                    </span>
                  </div>
                  <span className="text-[10px] font-bold text-zinc-500 font-mono">
                    BATCH: #HX-2026-X930
                  </span>
                </div>

                {/* Video Simulator Player */}
                <div className="relative h-64 sm:h-80 rounded-2xl overflow-hidden bg-zinc-900 border border-zinc-800 flex flex-col justify-between p-4">
                  
                  {/* Sourcing Crop Background Images for simulation */}
                  {activeVideo === 'farm' ? (
                    <img
                      src="public/images/organic-farm.jpg"
                      alt="Sourcing fields"
                      className="absolute inset-0 w-full h-full object-cover opacity-60"
                    />
                  ) : activeVideo === 'qc' ? (
                    <div className="absolute inset-0 bg-gradient-to-tr from-zinc-950 via-zinc-900 to-emerald-950/20 opacity-90 flex items-center justify-center p-8">
                      {/* Abstract visual representing a high-tech processing line */}
                      <div className="text-center space-y-4">
                        <div className="inline-block p-4 bg-emerald-500/10 rounded-full border border-emerald-500/20">
                          <CheckCircle2 className="w-12 h-12 text-emerald-400 animate-pulse" />
                        </div>
                        <div>
                          <p className="text-sm font-black text-white">HEALTHXPRESS PACKING FACILITY</p>
                          <p className="text-xs text-zinc-400 max-w-md">Live feed monitoring optical sorting technology separating organic Gujarati peanuts and moong sprouts from foreign particles with 99.9% accuracy.</p>
                        </div>
                      </div>
                    </div>
                  ) : (
                    <div className="absolute inset-0 bg-gradient-to-tr from-zinc-950 via-zinc-900 to-orange-950/20 opacity-90 flex items-center justify-center p-8">
                      {/* Abstract sprouting cook line */}
                      <div className="text-center space-y-4">
                        <div className="inline-block p-4 bg-orange-500/10 rounded-full border border-orange-500/20">
                          <Leaf className="w-12 h-12 text-orange-400 animate-spin-slow" />
                        </div>
                        <div>
                          <p className="text-sm font-black text-white">SPROUTING FLUIDIZED BEDS</p>
                          <p className="text-xs text-zinc-400 max-w-md">Monitoring temperature and humidity of sprouting green moong. Ideal sprout tail length achieved at 1.2 cm for peak bioavailability.</p>
                        </div>
                      </div>
                    </div>
                  )}

                  {/* Simulated Overlays */}
                  <div className="absolute top-4 left-4 z-10 bg-black/70 border border-zinc-800 px-3 py-1.5 rounded-lg flex items-center gap-1.5">
                    <div className="w-1.5 h-1.5 rounded-full bg-red-500 animate-ping"></div>
                    <span className="text-[10px] font-black tracking-widest text-zinc-200">SIMULATED STREAM</span>
                  </div>

                  <div className="absolute bottom-4 right-4 z-10 bg-black/70 border border-zinc-800 px-3 py-1.5 rounded-lg text-[9px] font-bold text-zinc-400 font-mono">
                    OCT 05, 2026 - 10:24:51 UTC
                  </div>

                  {/* Controls Overlay */}
                  <div className="absolute bottom-4 left-4 z-10 flex gap-2">
                    <button
                      onClick={() => setIsPlaying(!isPlaying)}
                      className="bg-white/10 hover:bg-white/20 p-2 rounded-lg text-white"
                    >
                      {isPlaying ? <Pause className="w-4 h-4" /> : <Play className="w-4 h-4" />}
                    </button>
                    <button
                      onClick={() => showToast('📷 Camera angle updated. High-fidelity optical check verified.')}
                      className="bg-white/10 hover:bg-white/20 p-2 rounded-lg text-white text-xs font-semibold flex items-center gap-1"
                    >
                      <RefreshCw className="w-3.5 h-3.5 animate-spin-slow" />
                      <span>Refresh Stream</span>
                    </button>
                  </div>

                </div>
              </div>

              {/* Feed select tabs */}
              <div className="grid grid-cols-3 gap-2 mt-4">
                <button
                  onClick={() => setActiveVideo('qc')}
                  className={`p-2.5 rounded-xl border text-xs font-semibold transition-all ${
                    activeVideo === 'qc' ? 'border-emerald-500 bg-emerald-950/20 text-emerald-400' : 'border-zinc-800 text-zinc-400 bg-zinc-900/30'
                  }`}
                >
                  🔴 Quality Control Line
                </button>
                <button
                  onClick={() => setActiveVideo('farm')}
                  className={`p-2.5 rounded-xl border text-xs font-semibold transition-all ${
                    activeVideo === 'farm' ? 'border-emerald-500 bg-emerald-950/20 text-emerald-400' : 'border-zinc-800 text-zinc-400 bg-zinc-900/30'
                  }`}
                >
                  🌾 Rajasthan Farm fields
                </button>
                <button
                  onClick={() => setActiveVideo('kitchen')}
                  className={`p-2.5 rounded-xl border text-xs font-semibold transition-all ${
                    activeVideo === 'kitchen' ? 'border-emerald-500 bg-emerald-950/20 text-emerald-400' : 'border-zinc-800 text-zinc-400 bg-zinc-900/30'
                  }`}
                >
                  🟢 Sprouting Kitchen
                </button>
              </div>

            </div>

            {/* FARM SOURCING BREAKDOWN (5-columns) */}
            <div className="lg:col-span-5 bg-zinc-950 border border-zinc-800 rounded-3xl p-6 flex flex-col justify-between">
              
              <div>
                <h3 className="text-sm font-extrabold text-white uppercase tracking-wider mb-4">
                  Verifiable Farm-to-Table Promise
                </h3>

                <div className="space-y-4">
                  {/* Ingredient Sourcing Row 1 */}
                  <div className="flex items-start gap-3 bg-zinc-900/40 p-3 rounded-2xl border border-zinc-900">
                    <span className="text-2xl">🌾</span>
                    <div>
                      <h4 className="text-xs font-bold text-white">Sprouted Organic Green Moong</h4>
                      <p className="text-[10px] text-zinc-500 mt-0.5">Sourced from: **Marathwada Farmers Cooperative, Maharashtra**</p>
                      <p className="text-[10px] text-zinc-400 mt-1">Chemical-free hydration & 24-hour sprouting cycles in customized humidity-fluidized chambers.</p>
                    </div>
                  </div>

                  {/* Ingredient Sourcing Row 2 */}
                  <div className="flex items-start gap-3 bg-zinc-900/40 p-3 rounded-2xl border border-zinc-900">
                    <span className="text-2xl">🥜</span>
                    <div>
                      <h4 className="text-xs font-bold text-white">Roasted Skin-On Bold Peanuts</h4>
                      <p className="text-[10px] text-zinc-500 mt-0.5">Sourced from: **Junagadh Groundnut Cooperative, Gujarat**</p>
                      <p className="text-[10px] text-zinc-400 mt-1">India's premium "Bold-40" peanut variety, slow-roasted to lock in healthy fats, niacin, and resveratrol.</p>
                    </div>
                  </div>

                  {/* Ingredient Sourcing Row 3 */}
                  <div className="flex items-start gap-3 bg-zinc-900/40 p-3 rounded-2xl border border-zinc-900">
                    <span className="text-2xl">🫘</span>
                    <div>
                      <h4 className="text-xs font-bold text-white">Dark High-Altitude Kidney Beans (Rajma)</h4>
                      <p className="text-[10px] text-zinc-500 mt-0.5">Sourced from: **Bhaderwah High Valley Growers, Jammu & Kashmir**</p>
                      <p className="text-[10px] text-zinc-400 mt-1">Cultivated in glacial mineral streams giving thin skins and higher water absorption ratios.</p>
                    </div>
                  </div>
                </div>
              </div>

              {/* Sourcing Pledge Badge */}
              <div className="mt-6 p-3 bg-emerald-500/10 border border-emerald-500/20 rounded-2xl flex items-center gap-3">
                <CheckCircle2 className="w-8 h-8 text-emerald-400 shrink-0" />
                <div>
                  <p className="text-xs font-black text-white uppercase tracking-wider">Lab Tested for Purity</p>
                  <p className="text-[10px] text-zinc-400 leading-snug">
                    Every 100kg batch is checked for heavy metal and pesticide residue. Scan the QR code on your container lid to view the chemical report!
                  </p>
                </div>
              </div>

            </div>
          </div>
        </section>


        {/* PILLAR 3: CONVENIENCE - FLEXIBLE PAUSE CALENDAR & CALENDAR INTEGRATION */}
        <section id="pause-calendar" className="scroll-mt-20 border-t border-zinc-900 pt-16">
          <div className="mb-8">
            <div className="flex items-center gap-2 mb-1">
              <Calendar className="w-5 h-5 text-indigo-400" />
              <span className="text-xs font-bold text-indigo-400 tracking-wider uppercase">PILLAR 3: TRANSPARENT UTILITY</span>
            </div>
            <h2 className="text-2xl sm:text-3xl font-extrabold text-white">
              Hustle-Free Pause Calendar & Scheduling
            </h2>
            <p className="text-zinc-400 text-xs sm:text-sm mt-1 max-w-2xl">
              Fear of subscription lock-in? Gone. Tap any week on the calendar below to pause delivery instantly. We even deliver around your deep work focus blocks.
            </p>
          </div>

          <div className="grid grid-cols-1 lg:grid-cols-12 gap-8">
            
            {/* PAUSE / SKIP CALENDAR (7-columns) */}
            <div className="lg:col-span-7 bg-zinc-950 border border-zinc-800 rounded-3xl p-6 flex flex-col justify-between">
              <div>
                <div className="flex flex-col sm:flex-row sm:items-center justify-between gap-4 mb-6">
                  <div>
                    <h3 className="text-sm font-extrabold text-white uppercase tracking-wider">
                      Single-Click Skip Calendar
                    </h3>
                    <p className="text-xs text-zinc-500">
                      Tap a week card to toggle your delivery status. We adjust your bill automatically.
                    </p>
                  </div>

                  {/* Price breakdown block */}
                  <div className="bg-zinc-900 px-4 py-2 rounded-xl border border-zinc-800 text-right shrink-0">
                    <p className="text-[9px] text-zinc-500 uppercase tracking-widest font-bold">Dynamic Monthly Cost</p>
                    <p className="text-base font-black text-white">
                      ${calculatedMonthlyPrice.toFixed(2)}{' '}
                      <span className="text-[10px] font-normal text-zinc-500">
                        ({activeWeeksCount}/8 active weeks)
                      </span>
                    </p>
                  </div>
                </div>

                {/* Calendar grid (8 weeks) */}
                <div className="grid grid-cols-2 sm:grid-cols-4 gap-3">
                  {calendarWeeks.map((week) => {
                    const isScheduled = week.status === 'scheduled';
                    return (
                      <button
                        key={week.id}
                        onClick={() => handleToggleWeek(week.id)}
                        className={`p-3 rounded-2xl border text-left transition-all relative flex flex-col justify-between min-h-[110px] group ${
                          isScheduled
                            ? 'border-zinc-800 hover:border-orange-500/40 bg-zinc-900/35'
                            : 'border-yellow-500/40 bg-yellow-950/10'
                        }`}
                      >
                        <div>
                          <div className="flex items-center justify-between">
                            <span className="text-[9px] font-extrabold text-zinc-500 font-mono uppercase">WEEK 0{week.id}</span>
                            <span className={`w-2 h-2 rounded-full ${isScheduled ? 'bg-emerald-500' : 'bg-yellow-500 animate-pulse'}`}></span>
                          </div>
                          
                          <p className="text-xs font-bold text-white mt-2 font-mono">{week.dateRange}</p>
                          <p className={`text-[10px] mt-1 font-semibold ${isScheduled ? 'text-zinc-400' : 'text-yellow-400'}`}>
                            {isScheduled ? '🟢 Active Delivery' : '✈️ Skip / Paused'}
                          </p>
                        </div>

                        <div className="mt-3 pt-1.5 border-t border-zinc-900/60 text-[9px] text-zinc-500 flex items-center justify-between">
                          <span>{isScheduled ? `$${baseCostPerBowl.toFixed(2)}` : '$0.00'}</span>
                          <span className="text-orange-400 font-bold opacity-0 group-hover:opacity-100 transition-all">
                            {isScheduled ? 'Tap to Pause' : 'Tap to Active'}
                          </span>
                        </div>
                      </button>
                    );
                  })}
                </div>
              </div>

              {/* Trust disclaimer */}
              <div className="border-t border-zinc-900 pt-4 mt-6 text-xs text-zinc-400 flex items-center gap-2">
                <ShieldAlert className="w-4 h-4 text-orange-400 shrink-0" />
                <span>
                  <strong>Hustler Promise:</strong> Zero penalties, zero calls to customer care. Paused weeks subtract 100% of their cost from your next monthly renewal automatically.
                </span>
              </div>
            </div>

            {/* GOOGLE CALENDAR DEEP WORK SYNC (5-columns) */}
            <div className="lg:col-span-5 bg-zinc-950 border border-zinc-800 rounded-3xl p-6 flex flex-col justify-between">
              
              <div>
                <div className="flex flex-col sm:flex-row sm:items-start justify-between gap-3 mb-4 pb-2 border-b border-zinc-900">
                  <div>
                    <h3 className="text-sm font-extrabold text-white uppercase tracking-wider">
                      Hustler Calendar Integration
                    </h3>
                    <p className="text-[10px] text-zinc-500">
                      Sync Focus Blocks so food never interrupts a meeting.
                    </p>
                  </div>

                  <button
                    onClick={() => {
                      setCalendarSync(!calendarSync);
                      showToast(calendarSync ? '🔌 Google Calendar connection disconnected!' : '🔌 Connected to deep_worker_calendar@gmail.com successfully!');
                    }}
                    className={`px-3 py-1.5 rounded-lg text-[10px] font-bold border transition-all ${
                      calendarSync
                        ? 'bg-indigo-600/10 border-indigo-500/40 text-indigo-400'
                        : 'bg-zinc-900 border-zinc-800 text-zinc-400'
                    }`}
                  >
                    {calendarSync ? 'CONNECTED ✓' : 'SYNC GOOGLE CAL'}
                  </button>
                </div>

                {calendarSync ? (
                  <div className="space-y-3">
                    <div className="bg-zinc-900/50 p-3 rounded-2xl border border-zinc-800">
                      
                      <div className="flex items-center justify-between mb-2">
                        <span className="text-xs font-bold text-zinc-300">Today's Focus Block Layout</span>
                        <span className="text-[10px] text-zinc-500">Google Calendar</span>
                      </div>

                      <div className="space-y-1.5">
                        {focusBlocks.map((block) => (
                          <div
                            key={block.id}
                            className={`p-2 rounded-xl border text-[11px] flex items-center justify-between ${
                              block.isFocus
                                ? 'border-indigo-500/30 bg-indigo-950/10'
                                : 'border-zinc-900 bg-zinc-950/50'
                            }`}
                          >
                            <div className="flex items-center gap-2">
                              <div className={`w-1.5 h-6 rounded ${block.isFocus ? 'bg-indigo-500' : 'bg-zinc-700'}`}></div>
                              <div>
                                <p className="font-bold text-zinc-200">{block.title}</p>
                                <p className="text-[9px] text-zinc-500">{block.start} - {block.end}</p>
                              </div>
                            </div>
                            {block.isFocus && (
                              <span className="text-[9px] font-extrabold text-indigo-400 bg-indigo-500/10 px-2 py-0.5 rounded border border-indigo-500/20">
                                DEEP WORK LOCK
                              </span>
                            )}
                          </div>
                        ))}
                      </div>

                      {/* Deliver around focus blocks toggle */}
                      <div className="mt-4 pt-3 border-t border-zinc-900 flex items-center justify-between gap-4">
                        <div>
                          <p className="text-xs font-bold text-white">Deliver around deep focus blocks</p>
                          <p className="text-[10px] text-zinc-500">Delivers exactly between standups and code sprints.</p>
                        </div>

                        <button
                          onClick={() => {
                            setDeliveryAroundFocus(!deliveryAroundFocus);
                            showToast(deliveryAroundFocus ? 'Delivery scheduled to standard slot (12:00 PM)' : 'Smart scheduling enabled! Delivery moved around deep work blocks.');
                          }}
                          className={`w-10 h-5 rounded-full p-0.5 transition-colors duration-300 ${
                            deliveryAroundFocus ? 'bg-indigo-500' : 'bg-zinc-800'
                          }`}
                        >
                          <div
                            className={`bg-zinc-950 w-4 h-4 rounded-full shadow-md transform transition-transform duration-300 ${
                              deliveryAroundFocus ? 'translate-x-5' : 'translate-x-0'
                            }`}
                          ></div>
                        </button>
                      </div>

                    </div>

                    {/* Calculated Delivery Time Result */}
                    <div className="bg-indigo-950/20 border border-indigo-500/30 rounded-2xl p-3.5">
                      <p className="text-[10px] font-black text-indigo-400 uppercase tracking-wider">DETERMINED DELIVERY SLOT</p>
                      <p className="text-sm font-extrabold text-white mt-1">
                        {deliveryTimeText}
                      </p>
                      <p className="text-[10px] text-zinc-400 mt-1">
                        Your rider gets a push notification to lock-box deliver at this specific window to prevent cell buzzer interruptions.
                      </p>
                    </div>

                  </div>
                ) : (
                  <div className="p-8 text-center border-2 border-dashed border-zinc-800 rounded-2xl space-y-3">
                    <CalendarIcon className="w-8 h-8 text-zinc-600 mx-auto" />
                    <div>
                      <p className="text-xs font-bold text-zinc-300">Google Calendar Synced Scheduling</p>
                      <p className="text-[10px] text-zinc-500 max-w-xs mx-auto mt-0.5">
                        Synchronize your work hours and meeting times to avoid lunch deliveries landing mid-presentation.
                      </p>
                    </div>
                    <button
                      onClick={() => {
                        setCalendarSync(true);
                        showToast('🔌 Google Calendar connected! Daily agenda loaded.');
                      }}
                      className="bg-indigo-600 hover:bg-indigo-500 text-zinc-950 font-black px-4 py-2 rounded-xl text-[10px] tracking-wider uppercase inline-block mx-auto"
                    >
                      Sync Now
                    </button>
                  </div>
                )}

              </div>

              {/* Delivery customizer notes */}
              <div className="text-[10px] text-zinc-500 mt-4 leading-normal">
                💡 **Pro Hustler Tip:** Drag meeting slots directly on your Google Calendar; our driver routing system updates delivery windows in real-time up to 9:30 AM every morning.
              </div>

            </div>
          </div>
        </section>


        {/* CELEBRATION MODAL FOR STREAK 3 - UNLOCKING THE FLASK */}
        {showFlaskCelebration && (
          <div className="fixed inset-0 z-50 bg-black/90 backdrop-blur-md flex items-center justify-center p-4">
            <div className="bg-zinc-950 rounded-3xl border-2 border-orange-500/50 max-w-md w-full p-6 sm:p-8 relative text-center overflow-hidden shadow-2xl shadow-orange-500/20 animate-scaleIn">
              
              {/* Confetti background simulation */}
              <div className="absolute inset-0 pointer-events-none opacity-40">
                <div className="absolute top-10 left-1/4 w-2 h-2 bg-orange-500 rounded-full animate-ping"></div>
                <div className="absolute top-20 right-1/4 w-3 h-3 bg-yellow-400 rounded-full animate-bounce"></div>
                <div className="absolute bottom-12 left-1/3 w-1.5 h-1.5 bg-red-500 rounded-full animate-ping"></div>
              </div>

              <span className="text-4xl animate-bounce inline-block mb-3">🏆</span>
              
              <h2 className="text-2xl font-black text-white">
                STREAK ACHIEVED!
              </h2>
              <p className="text-xs text-orange-400 font-extrabold uppercase tracking-widest mt-1">
                3-Month Hustler Milestone
              </p>

              {/* Graphic Flask Display */}
              <div className="my-6 p-4 bg-zinc-900 rounded-2xl border border-zinc-800 inline-block relative group">
                <div className="absolute -inset-1 rounded-xl bg-orange-500 opacity-20 blur group-hover:opacity-40 transition"></div>
                <div className="relative text-7xl select-none mb-1">🧪</div>
                <span className="text-[10px] font-black tracking-widest text-zinc-400 uppercase block">THE "HUSTLER" FLASK</span>
              </div>

              <p className="text-xs text-zinc-300 leading-relaxed mb-6">
                Congratulations! You’ve locked in <strong>90 consecutive days of clean, high-performance eating</strong>. Your limited-edition double-walled matte black insulated flask has been added to your upcoming parcel free of charge!
              </p>

              <div className="space-y-2">
                <button
                  onClick={() => {
                    setShowFlaskCelebration(false);
                    showToast('🎁 Hustler Flask successfully included in your upcoming parcel shipping Oct 12!');
                  }}
                  className="w-full bg-gradient-to-r from-orange-500 to-red-500 hover:from-orange-400 hover:to-red-400 text-zinc-950 font-black py-3 rounded-xl text-xs tracking-wider uppercase shadow-lg shadow-orange-500/20"
                >
                  Hell Yes, Add to Package!
                </button>
                <button
                  onClick={() => setShowFlaskCelebration(false)}
                  className="w-full text-zinc-500 hover:text-zinc-400 text-[10px] font-bold"
                >
                  Close Milestones View
                </button>
              </div>

            </div>
          </div>
        )}

      </main>

      {/* FOOTER */}
      <footer className="bg-zinc-950 border-t border-zinc-900 mt-24 py-12">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="grid grid-cols-1 md:grid-cols-4 gap-8">
            <div className="space-y-4">
              <div className="flex items-center gap-2.5">
                <HXLogo className="w-10 h-10" />
                <div className="flex flex-col leading-none">
                  <span className="font-extrabold text-base tracking-wider text-emerald-400">
                    HEALTH<span className="text-amber-400">X</span>PRESS
                  </span>
                  <span className="text-[8px] font-bold tracking-[0.2em] text-zinc-500 mt-1">
                    EAT CLEAN. TRAIN HARD.
                  </span>
                </div>
              </div>
              <p className="text-xs text-zinc-500 leading-relaxed">
                <span className="text-emerald-400 font-bold">SINCE 2024.</span> The premium high-performance 30G Protein Bowl subscription designed for desk athletes, gym-hustlers, and builders. Powered by our signature <span className="text-amber-400 font-semibold">Peanut + Moong + Rajma</span> trio.
              </p>
              <div className="flex flex-wrap gap-2">
                <span className="text-[10px] bg-emerald-500/10 border border-emerald-500/20 px-2 py-1 rounded text-emerald-400 font-bold">VEG &amp; NON-VEG</span>
                <span className="text-[10px] bg-amber-500/10 border border-amber-500/20 px-2 py-1 rounded text-amber-400 font-bold">100% CUSTOMISABLE</span>
                <span className="text-[10px] bg-zinc-900 border border-zinc-800 px-2 py-1 rounded text-zinc-400 font-bold">PESTICIDE CLEAN</span>
              </div>
            </div>

            <div>
              <h4 className="text-xs font-bold text-white uppercase tracking-widest mb-3">Our Three Pillars</h4>
              <ul className="space-y-2 text-xs text-zinc-500">
                <li><span className="text-orange-500 font-bold mr-1">01.</span> Hyper-Personalization Quiz</li>
                <li><span className="text-orange-500 font-bold mr-1">02.</span> Macro-Locked Customizer</li>
                <li><span className="text-orange-500 font-bold mr-1">03.</span> Loyalty Arena Gamification</li>
                <li><span className="text-orange-500 font-bold mr-1">04.</span> Deep Work Calendar Dispatch</li>
              </ul>
            </div>

            <div>
              <h4 className="text-xs font-bold text-white uppercase tracking-widest mb-3">Sourced Cooperatives</h4>
              <ul className="space-y-2 text-xs text-zinc-500">
                <li>• Junagadh Bold Peanuts (Gujarat)</li>
                <li>• Marathwada Moong Sprout Co-op (Maharashtra)</li>
                <li>• Bhaderwah High Valley Rajma (Jammu)</li>
                <li>• Organic Baby Greens (Himachal Hills)</li>
              </ul>
            </div>

            <div>
              <h4 className="text-xs font-bold text-white uppercase tracking-widest mb-3">Community Hub</h4>
              <p className="text-xs text-zinc-500 leading-normal mb-3">
                Join our Discord with over 12,400+ developers, designers, and high-energy builders tracking daily focus times.
              </p>
              <button 
                onClick={() => showToast('💬 Join link dispatched! Looking forward to seeing you in the chat!')}
                className="w-full bg-zinc-900 border border-zinc-800 text-zinc-300 hover:text-white hover:bg-zinc-800 font-bold py-2 px-3 rounded-xl text-xs transition"
              >
                Join Discord Channel
              </button>
            </div>
          </div>

          <div className="border-t border-zinc-900 mt-12 pt-6 text-center text-xs text-zinc-600 flex flex-col sm:flex-row justify-between items-center gap-4">
            <p>© 2026 HealthXpress Food Systems. All rights, macros, and micro-fuels reserved.</p>
            <div className="flex gap-4">
              <a href="#" className="hover:text-zinc-400">Pesticide Certification Reports</a>
              <a href="#" className="hover:text-zinc-400">Rider Tip Agreements</a>
              <a href="#" className="hover:text-zinc-400">Deep Work API Docs</a>
            </div>
          </div>
        </div>
      </footer>

    </div>
  );
}
