# Passage Plan Singapore – MT TITANIA

Aplikasi offline (satu file HTML) untuk menyusun passage plan di perairan pelabuhan Singapura (in-port, via fairway).

**Buka:** https://sailor-90.github.io/singapore-passage-plan/

## Features (4 pages, English UI)
1. **Data** – ship, voyage, date/ETD, draft fwd/aft → mean draft, displacement (hydrostatic table), air draught (32.3 m − aft draft); Master, 2nd Master, Chief Officer, Chief Engineer (printed in each form's signature boxes, kept on the device); weekly ENC/NM update.
2. **Route** – From / To (anchorage, berth, or manual position), route version from the ship's passage plan files or built from the waypoint network, planned speed, speed per leg.
3. **Plan** – waypoint cards (position, ETA, course, distance, leg speed, UKC, ENC cell, fixing, remarks/PI): edit, delete, insert; remarks can be reused for future routes through the same point. Excel (ship's Form N 03) and PDF (passage plan, passage planning checklist N-03A, arrival/departure checklist N-01/N-02, pilot card N-04). More: save/open routes, save to WP network, export/import data.
4. **Chart** – vector chart of the port (MPA PMC 02/2018 + PMC 03/2025, SPI Oct 2026): anchorages, fairways, TSS, port limits, buoys, terminals and berths; search; layers; measure; **Edit route** – drag waypoints, tap ＋ to insert, select to edit/delete. Chart and plan share the same route.

## Install di HP (offline)
- iPhone (Safari): buka link → Share → *Add to Home Screen*.
- Android (Chrome): menu ⋮ → *Install app*.
Setelah dibuka sekali saat online, aplikasi tetap jalan tanpa internet.

## Update
Ganti `index.html`, lalu naikkan `VERSION` di `sw.js` (mis. `v2`) agar HP mengambil versi baru.

> Alat bantu perencanaan. Wajib diverifikasi pada ENC/ECDIS terkoreksi, Notices to Mariners dan Port Marine Notices terbaru.
