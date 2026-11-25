# PulseBI — Sales & Revenue Analysis Dashboard

A single-file, zero-backend interactive Sales & Revenue Analysis Dashboard (`index.html`) built with HTML, CSS, JavaScript, Chart.js, PapaParse, and SheetJS.

## How to Use

1. **Open the Dashboard**
   Double-click `index.html` to open it in any modern browser (Chrome, Edge, Firefox, Safari). No web server or build step is required.
   > **Note:** An internet connection is needed on first load to fetch Chart.js, PapaParse, and SheetJS from jsDelivr CDN.

2. **Explore Immediately with Sample Data**
   On startup, the dashboard loads **520 deterministic sample transactions spanning 18 months** (`June 2025 – November 2026`). Click **Sample Data** in the top bar anytime to restore it.

3. **Import Your Own Data**
   Click **Import / Data Source**:
   - **CSV / Excel Upload**: Drag and drop a `.csv`, `.xlsx`, or `.xls` file (or click **Choose File**).
   - **Column Mapping & Preview**: Review auto-detected column mappings, adjust any dropdowns if needed, inspect the first 10 rows, check validation messages, and click **Apply Data to Dashboard**.
   - **Database / REST API**: Copy the example SQL query to export results from your database to CSV, or paste a CORS-enabled JSON/CSV REST API URL to fetch live data directly.

4. **Filter, Cross-Filter & Export**
   - Use the **left sidebar** for Date Range presets (`All`, `3M`, `6M`, `12M`, `YTD`), custom date pickers, and multi-select filters for **Category**, **Region**, and **Product**.
   - **Click any chart element** (a trend point, category slice, product bar, region bar, or Month × Weekday heatmap cell) to cross-filter the entire dashboard. Click the same element again or dismiss the pill in the status bar to clear it.
   - Click **Export CSV** to download the currently filtered rows.
   - Toggle **Light / Dark Mode** using the moon/sun button in the top-right corner.

---

## Expected Data Format

Column names are matched **case-insensitively** with common aliases (for example, `order_id`, `sales`, `qty`, `cogs`), and can also be manually mapped in the import modal.

| Column | Required | Description & Accepted Formats |
| :--- | :--- | :--- |
| **Date** | **Yes** | ISO (`2026-05-14`), US (`05/14/2026`), or native Excel serial/date values. |
| **Product** | **Yes** | Product or SKU name. |
| **Revenue** | Conditional* | Total line-item sales amount. Supports `$`, `,`, and `(100.00)` negatives. |
| **Quantity** | Conditional* | Units sold (defaults to `1` if omitted). |
| **Unit Price** | Conditional* | Price per unit. *If `Revenue` is omitted, `Quantity` + `Unit Price` must both be mapped so `Revenue = Quantity × Unit Price` is computed automatically.* |
| **Order ID** | Optional | Used to count unique orders and compute Average Order Value (falls back to row IDs if omitted). |
| **Category** | Optional | Product category for donut share & filtering (defaults to `General`). |
| **Region** | Optional | Sales territory/region (defaults to `All Regions`). |
| **Customer** | Optional | Customer or account name (defaults to `N/A`). |
| **Cost** | Optional | Total line-item cost / COGS. When present, **Profit Margin %** and net profit are unlocked. |

Rows with missing or unparseable dates or revenue are safely skipped and surfaced in a dismissible data-quality banner.

---

## How to Extend It

Because everything lives in clean, commented sections inside `index.html`, extending the dashboard is straightforward:

1. **Add a New KPI Card**
   - Add a `<article class="kpi-card">` block in the `.kpi-grid` HTML section.
   - Compute your metric inside `computeAnalytics(rows)` and update the DOM inside `renderKPIs(analytics)`.

2. **Add a New Chart or Dimension (e.g., Sales Rep or Channel)**
   - Add the new field definition to `SCHEMA_FIELDS` (including aliases for auto-detection) and map it inside `normalizeAndValidateRows()`.
   - Aggregate by the new field in `computeAnalytics(rows)` using a `Map` and render a new Chart.js instance inside `renderCharts(analytics)`.

3. **Connect to a Live Backend / Authenticated API**
   - Update `fetchSalesFromAPI(url)` to include custom headers (such as `Authorization: Bearer <token>`) or transform nested JSON responses before calling `stageParsedDataset(headers, rows, name)`.

4. **Make It 100% Offline (Air-Gapped)**
   - Download `papaparse.min.js`, `xlsx.full.min.js`, and `chart.umd.min.js` and inline their contents inside `<script>` tags in `index.html`, replacing the three CDN `<script src="...">` tags at the top of the file.
