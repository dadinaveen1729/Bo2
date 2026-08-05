# Boost Oxygen  Package Scan Confirm System

Built from scratch. No vendor. No IT team. Just a problem that needed solving.

---

## The Story

Packages were leaving our warehouse every day and we had no way to prove they were scanned before the truck left. If a customer called saying their order never arrived, we had nothing. No timestamp. No record. No proof.

So I built one.

This is a full warehouse verification platform I designed, coded, and deployed while working as the person responsible for making sure fulfillment actually runs. Not a side project. Not a school assignment. Something real people use every day to do their jobs better.

---

## What It Does

Every package that goes on a carrier truck at Boost Oxygen gets scanned through this system before it leaves. The moment a barcode is scanned, the app cross-references it against two live data sources, confirms the match, logs a timestamp, and gives the team an instant green or red signal.

Green means confirmed. Red means stop. Yellow means find your lead.

That is the whole job. And it works.

---

## The Technical Build

**Frontend**
- Vanilla HTML, CSS, JavaScript
- Quagga.js barcode scanner library running at 25fps with single-read config tuned for Inateck Bluetooth hardware
- Web Speech API for voice alerts so workers do not have to look at a screen to know the result
- PIN-protected scan tab to prevent accidental access
- Fully responsive, works on any device in a warehouse environment

**Backend**
- Google Apps Script serving as a live API endpoint
- Google Sheets as the operational database with two separate sheets: Scan Checklist and Scan History
- Column-level text formatting locked on tracking number fields to prevent Google Sheets from corrupting long digit strings
- Date handling written with custom formatDateSafe and formatTimeSafe helpers to eliminate 1899 conversion bugs

**Data Pipeline**
- Python loader script running at noon and 1:30 PM daily
- Ingests two CSV sources: RF Smart export from ShipStation and NetSuite saved search
- Auto-detects file type, deduplicates by tracking number, RF Smart takes priority with NetSuite filling the gaps
- Handles UPS Mail Innovations and SmartPost packages that only appear in NetSuite using 420-prefix detection

**AI and ML Layer**
- Barcode input cleaning using pattern recognition to strip GS1-128 application identifiers that scanner hardware prepends to raw scan data
- Regex-based carrier detection to identify USPS, UPS, and FedEx tracking number formats automatically
- NOT FOUND false positive suppression using an 8-second cancellation window so a misread scan followed by a correct scan does not pollute the history log
- OTDR risk scoring logic built separately in Python that predicts which shipments will miss Amazon delivery windows before the customer notices

**Infrastructure**
- Hosted on GitHub Pages, zero cost, always live
- Backup deployment on Netlify at bo2.netlify.app
- No server, no maintenance window, no downtime

---

## The Scan Confirm Flow

```
RF Smart CSV + NetSuite CSV
          |
    Python Loader (noon + 1:30 PM)
          |
    Google Sheets (live database)
          |
    Web App reads via Apps Script API
          |
    Worker scans barcode with Inateck scanner
          |
    cleanTrackingNumber() strips GS1-128 prefix
          |
    Cross-reference against loaded packages
          |
    GREEN  /  RED  /  YELLOW
    Confirmed / Duplicate / Not Found
          |
    Timestamp logged to Scan History
          |
    Dashboard + Missing + Report tabs update live
```

---

## Tabs in the App

**Scan** — the live scanning interface. Barcode goes in, result comes back instantly with voice alert and color flash.

**Dashboard** — real-time summary of confirmed, missing, and duplicate counts split by today and all loaded dates.

**Missing** — every package that has not been scanned yet with a date filter so leads can sort by day.

**History** — permanent log of every scan ever made. Searchable by tracking number, date, or carrier.

**Search** — type any partial tracking number and results filter in real time.

**Report** — exportable view for end of day reconciliation.

---

## The Problems This Solved

Before this system existed, reconciliation meant manually comparing a printed list against a spreadsheet after the truck already left. If something was missing nobody knew until a customer called.

Now the team knows before the truck backs in. Missing packages show up on a screen with a timestamp. Duplicates get caught at the scanner. Every confirmation is logged permanently.

If a customer says their package never arrived, we can tell them the exact second it was scanned, which device scanned it, and what time it went on the truck.

---

## What I Learned Building This

Carrier barcodes are not just tracking numbers. USPS Ground Advantage labels encode GS1-128 application identifiers that scanner hardware reads as part of the barcode data stream. The tracking number embedded in the physical label barcode can differ from the tracking number stored in ShipStation because UPS uses a different field for the actual scannable barcode versus the human-readable number. These are the kinds of things you only discover when real scanners hit real labels in a real warehouse.

I fixed every one of them.

---

## Built By

Naveen Dadi
Boost Oxygen LLC, Milford CT

Reporting to Rob Neuner, CEO and Mike Grice, COO

Supply chain and business analytics background. I build the tools my team actually needs instead of waiting for a vendor to sell us something that almost fits.

This system runs in production every single day.

---

*Pick Accurately. Pack Securely. Scan Everything.*
