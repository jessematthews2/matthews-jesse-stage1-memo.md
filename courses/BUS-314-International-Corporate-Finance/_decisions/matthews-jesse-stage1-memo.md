const {
  Document, Packer, Paragraph, TextRun, AlignmentType,
  HeadingLevel, BorderStyle, LevelFormat, WidthType,
  Table, TableRow, TableCell, ShadingType
} = require('docx');
const fs = require('fs');

const today = "April 4, 2026";

// Helper: divider paragraph
function divider() {
  return new Paragraph({
    border: { bottom: { style: BorderStyle.SINGLE, size: 6, color: "1F4E79", space: 1 } },
    spacing: { after: 160 }
  });
}

// Helper: body paragraph
function body(text, opts = {}) {
  return new Paragraph({
    children: [new TextRun({ text, font: "Arial", size: 22, ...opts })],
    spacing: { after: 160 },
    alignment: AlignmentType.JUSTIFIED
  });
}

// Helper: label + value inline
function metaRow(label, value) {
  return new Paragraph({
    children: [
      new TextRun({ text: label, font: "Arial", size: 22, bold: true }),
      new TextRun({ text: value, font: "Arial", size: 22 })
    ],
    spacing: { after: 80 }
  });
}

const doc = new Document({
  styles: {
    default: { document: { run: { font: "Arial", size: 22 } } },
    paragraphStyles: [
      {
        id: "Heading1", name: "Heading 1", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 28, bold: true, font: "Arial", color: "1F4E79" },
        paragraph: { spacing: { before: 320, after: 120 }, outlineLevel: 0 }
      },
      {
        id: "Heading2", name: "Heading 2", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 24, bold: true, font: "Arial", color: "2E75B6" },
        paragraph: { spacing: { before: 240, after: 100 }, outlineLevel: 1 }
      }
    ]
  },
  numbering: {
    config: [
      {
        reference: "bullets",
        levels: [{
          level: 0, format: LevelFormat.BULLET, text: "\u2022", alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } }, run: { font: "Arial", size: 22 } }
        }]
      }
    ]
  },
  sections: [{
    properties: {
      page: {
        size: { width: 12240, height: 15840 },
        margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 }
      }
    },
    children: [

      // ── TITLE BLOCK ──────────────────────────────────────────────
      new Paragraph({
        children: [new TextRun({ text: "DECISION MEMO", font: "Arial", size: 36, bold: true, color: "1F4E79", allCaps: true })],
        alignment: AlignmentType.CENTER,
        spacing: { after: 80 }
      }),
      new Paragraph({
        children: [new TextRun({ text: "Exxon Mobil Corporation (XOM) — Comprehensive Ratio Analysis", font: "Arial", size: 26, italics: true, color: "2E75B6" })],
        alignment: AlignmentType.CENTER,
        spacing: { after: 240 }
      }),
      divider(),

      // ── METADATA ─────────────────────────────────────────────────
      metaRow("Prepared by:  ", "[Analyst Name]"),
      metaRow("Submitted to: ", "[CFO Name], Chief Financial Officer"),
      metaRow("Date:         ", today),
      metaRow("Version:      ", "1.0"),
      metaRow("LLM Used:     ", "Claude Sonnet 4.6 (drafting support)"),

      new Paragraph({ spacing: { after: 200 } }),
      divider(),

      // ── SECTION 1 ─────────────────────────────────────────────────
      new Paragraph({ text: "1.  Executive Summary", heading: HeadingLevel.HEADING_1 }),

      body(
        "This memo establishes the analytical framework for a comprehensive financial ratio analysis of Exxon Mobil Corporation (XOM), one of the world's largest publicly traded energy companies by market capitalization and revenue. The objective is to evaluate XOM's financial health and operational performance across six ratio categories—Performance, Profitability, Efficiency, Leverage, Liquidity, and DuPont Decomposition—using audited data from the most recent fiscal year. The analysis will yield a data-driven assessment suitable for informing capital allocation decisions, peer benchmarking, and strategic positioning recommendations. Key deliverables include a full ratio dashboard, year-over-year trend commentary, and a DuPont attribution breakdown isolating the primary drivers of return on equity."
      ),

      new Paragraph({ spacing: { after: 120 } }),
      divider(),

      // ── SECTION 2 ─────────────────────────────────────────────────
      new Paragraph({ text: "2.  Company Background & Objectives", heading: HeadingLevel.HEADING_1 }),

      body(
        "Exxon Mobil Corporation is a vertically integrated supermajor operating across upstream exploration and production, downstream refining and marketing, and chemicals. Headquartered in Spring, Texas, XOM is listed on the NYSE and is a constituent of the S&P 500. The company's scale, global footprint, and capital-intensive business model make ratio analysis especially instructive: margins and returns are sensitive to commodity cycles, leverage decisions carry long-term balance sheet implications, and liquidity management is critical given the company's substantial debt obligations and dividend commitment."
      ),
      body(
        "The purpose of this analysis is threefold: (1) to assess current financial performance relative to historical benchmarks, (2) to compare XOM against integrated energy peers such as Chevron (CVX) and Shell (SHEL), and (3) to surface actionable insights to support ongoing investment and risk management deliberations."
      ),

      new Paragraph({ spacing: { after: 120 } }),
      divider(),

      // ── SECTION 3 ─────────────────────────────────────────────────
      new Paragraph({ text: "3.  Analytical Approach", heading: HeadingLevel.HEADING_1 }),

      body("The analysis will be structured across the following six ratio categories:"),

      new Paragraph({ spacing: { after: 80 } }),

      // Table of ratio categories
      new Table({
        width: { size: 9360, type: WidthType.DXA },
        columnWidths: [2600, 6760],
        rows: [
          new TableRow({
            tableHeader: true,
            children: [
              new TableCell({
                width: { size: 2600, type: WidthType.DXA },
                shading: { fill: "1F4E79", type: ShadingType.CLEAR },
                margins: { top: 80, bottom: 80, left: 120, right: 120 },
                children: [new Paragraph({ children: [new TextRun({ text: "Category", bold: true, font: "Arial", size: 22, color: "FFFFFF" })] })]
              }),
              new TableCell({
                width: { size: 6760, type: WidthType.DXA },
                shading: { fill: "1F4E79", type: ShadingType.CLEAR },
                margins: { top: 80, bottom: 80, left: 120, right: 120 },
                children: [new Paragraph({ children: [new TextRun({ text: "Analytical Focus", bold: true, font: "Arial", size: 22, color: "FFFFFF" })] })]
              })
            ]
          }),
          ...[
            ["Performance", "Revenue growth, earnings per share, return on assets, and return on equity to assess top-line and bottom-line trajectory."],
            ["Profitability", "Gross, operating, EBITDA, and net profit margins to evaluate cost structure and pricing power across business segments."],
            ["Efficiency", "Asset turnover, inventory turnover, and receivables turnover ratios to gauge operational effectiveness and working capital management."],
            ["Leverage", "Debt-to-equity, interest coverage, and debt-to-EBITDA ratios to quantify financial risk and capacity to service obligations."],
            ["Liquidity", "Current ratio, quick ratio, and cash ratio to assess short-term solvency and the adequacy of liquid assets relative to near-term liabilities."],
            ["DuPont Decomposition", "Disaggregation of ROE into net profit margin, asset turnover, and financial leverage to isolate primary performance drivers."],
          ].map((row, i) => new TableRow({
            children: [
              new TableCell({
                width: { size: 2600, type: WidthType.DXA },
                shading: { fill: i % 2 === 0 ? "DEEAF1" : "FFFFFF", type: ShadingType.CLEAR },
                margins: { top: 80, bottom: 80, left: 120, right: 120 },
                children: [new Paragraph({ children: [new TextRun({ text: row[0], bold: true, font: "Arial", size: 22, color: "1F4E79" })] })]
              }),
              new TableCell({
                width: { size: 6760, type: WidthType.DXA },
                shading: { fill: i % 2 === 0 ? "DEEAF1" : "FFFFFF", type: ShadingType.CLEAR },
                margins: { top: 80, bottom: 80, left: 120, right: 120 },
                children: [new Paragraph({ children: [new TextRun({ text: row[1], font: "Arial", size: 22 })] })]
              })
            ]
          }))
        ]
      }),

      new Paragraph({ spacing: { after: 160 } }),

      body(
        "Primary data will be sourced from XOM's audited annual report (Form 10-K), SEC EDGAR filings, and supplementary investor relations disclosures. All ratios will be calculated from standardized financial statements to ensure comparability. Where applicable, three-year trend data will be incorporated to contextualize current period results."
      ),

      new Paragraph({ spacing: { after: 120 } }),
      divider(),

      // ── SECTION 4 ─────────────────────────────────────────────────
      new Paragraph({ text: "4.  Limitations & Next Steps", heading: HeadingLevel.HEADING_1 }),

      body("The following constraints and assumptions should be noted:"),

      new Paragraph({
        numbering: { reference: "bullets", level: 0 },
        children: [new TextRun({ text: "Ratio analysis reflects historical performance and does not constitute a forward-looking forecast or investment recommendation.", font: "Arial", size: 22 })],
        spacing: { after: 80 }
      }),
      new Paragraph({
        numbering: { reference: "bullets", level: 0 },
        children: [new TextRun({ text: "Segment-level data may be subject to allocation methodologies that limit direct comparability across peers.", font: "Arial", size: 22 })],
        spacing: { after: 80 }
      }),
      new Paragraph({
        numbering: { reference: "bullets", level: 0 },
        children: [new TextRun({ text: "Commodity price volatility can distort period-over-period comparisons; macro context will be noted where material.", font: "Arial", size: 22 })],
        spacing: { after: 160 }
      }),

      body(
        "Immediate next steps include data extraction and normalization from the most recent 10-K, calculation and validation of all six ratio categories, and drafting of the full analytical memo with peer benchmarks. The completed analysis is expected within [X business days] and will be distributed to relevant stakeholders upon CFO review and approval."
      ),

      new Paragraph({ spacing: { after: 120 } }),
      divider(),

      // ── SECTION 5 ─────────────────────────────────────────────────
      new Paragraph({ text: "5.  References", heading: HeadingLevel.HEADING_1 }),

      body("Exxon Mobil Corporation. (2025). Annual Report on Form 10-K. U.S. Securities and Exchange Commission, EDGAR Filing System. https://www.sec.gov/cgi-bin/browse-edgar?action=getcompany&CIK=XOM"),
      body("Penman, S. H. (2013). Financial Statement Analysis and Security Valuation (5th ed.). McGraw-Hill Education."),
      body("Damodaran, A. (2024). Damodaran Online: Data. Stern School of Business, New York University. https://pages.stern.nyu.edu/~adamodar/"),
      body("CFA Institute. (2024). CFA Program Curriculum: Financial Statement Analysis. CFA Institute Publications."),
    ]
  }]
});

Packer.toBuffer(doc).then(buf => {
  fs.writeFileSync("/home/claude/XOM_Decision_Memo.docx", buf);
  console.log("Done.");
});
