/* UniBudget ULIMA PRO
   Funciones:
   - guardar en localStorage
   - CRUD transacciones
   - filtros por fecha/categoría/tipo
   - gráfico (Chart.js)
   - presupuesto mensual + alert
   - export CSV, descargar
   - conversor PEN/USD (tipo fijo)
   - sugerencias automáticas
   - modo claro/oscuro (toggle)
*/

const STORAGE_KEY = "unibudget_ulima_pro_v1";
const BUDGET_KEY = "unibudget_ulima_budget_v1";
const NAME_KEY = "unibudget_ulima_name_v1";
const DEFAULT_RATE = 0.27; // ejemplo: 1 PEN = 0.27 USD (puedes ajustar)

/* State */
let txs = [];
let chart = null;
let budget = Number(localStorage.getItem(BUDGET_KEY)) || 0;

/* Elements */
const el = {
  txList: document.getElementById("txList"),
  totalIncome: document.getElementById("totalIncome"),
  totalExpense: document.getElementById("totalExpense"),
  balance: document.getElementById("balance"),
  txCount: document.getElementById("txCount"),
  pieCanvas: document.getElementById("pieChart"),
  chartLegend: document.getElementById("chartLegend"),
  budgetFill: document.getElementById("budgetFill"),
  budgetMeta: document.getElementById("budgetMeta"),
  toast: document.getElementById("toast")
};

/* Init */
document.addEventListener("DOMContentLoaded", () => {
  // set defaults
  document.getElementById("date").value = today();
  document.getElementById("from").value = monthStart();
  document.getElementById("to").value = today();
  document.getElementById("studentName").value = localStorage.getItem(NAME_KEY) || "";

  // load txs
  load();

  // attach events
  document.getElementById("txForm").addEventListener("submit", onSubmit);
  document.getElementById("btnClear").addEventListener("click", () => {
    document.getElementById("txForm").reset();
    document.getElementById("date").value = today();
  });
  document.getElementById("btnExport").addEventListener("click", exportCSV);
  document.getElementById("search").addEventListener("input", render);
  document.getElementById("applyFilters").addEventListener("click", render);
  document.getElementById("setBudget").addEventListener("click", setBudgetFromInput);
  document.getElementById("budgetInput").value = budget || "";
  document.getElementById("convBtn").addEventListener("click", handleConvert);
  document.getElementById("resetData").addEventListener("click", resetAll);
  document.getElementById("suggestBtn").addEventListener("click", showSuggestions);
  document.getElementById("downloadCSV").addEventListener("click", exportCSV);
  document.getElementById("studentName").addEventListener("change", (e)=> {
    localStorage.setItem(NAME_KEY, e.target.value || "");
  });

  document.getElementById("themeToggle").addEventListener("click", toggleTheme);

  // initial render
  render();
  renderChart();
  updateBudgetUI();
});

/* Storage */
function load(){
  try {
    const raw = localStorage.getItem(STORAGE_KEY);
    txs = raw ? JSON.parse(raw) : sampleData();
  } catch(e){
    console.error(e);
    txs = sampleData();
  }
}
function save(){
  localStorage.setItem(STORAGE_KEY, JSON.stringify(txs));
}

/* Sample */
function sampleData(){
  // 🔧 ahora no genera transacciones por defecto
  return [];
}

