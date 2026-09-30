
(function () {
    'use strict';

    function names(wrap) {
        return (wrap.getAttribute('data-tier-names') || '').split('|');
    }

    function toInt(value, fallback) {
        var n = parseInt(value, 10);
        return isNaN(n) ? fallback : n;
    }

    function setTier(wrap, tier) {
        var max = toInt(wrap.getAttribute('data-max-tier'), 0);
        tier = Math.max(0, Math.min(max, tier));
        wrap.setAttribute('data-tier', String(tier));

        var fieldId = wrap.getAttribute('data-state-field');
        var field = fieldId ? document.getElementById(fieldId) : null;
        if (field) { field.value = String(tier); }

        var tierNames = names(wrap);
        var id = wrap.id;

        document.querySelectorAll('[data-grid-more="' + id + '"]').forEach(function (btn) {
            btn.disabled = tier >= max;
            var next = tierNames[tier + 1];
            btn.title = next ? 'Show ' + next + ' columns' : 'All columns shown';
            var text = btn.querySelector('[data-grid-more-text]');
            if (text) { text.textContent = next ? next : 'All shown'; }
        });

        document.querySelectorAll('[data-grid-less="' + id + '"]').forEach(function (btn) {
            btn.disabled = tier <= 0;
            btn.title = tier > 0 ? 'Hide ' + (tierNames[tier] || '') + ' columns' : '';
        });

        document.querySelectorAll('[data-grid-tier-label="' + id + '"]').forEach(function (label) {
            label.textContent = tierNames.slice(0, tier + 1).filter(Boolean).join(' + ');
        });
    }

    document.addEventListener('click', function (e) {
        var more = e.target.closest('[data-grid-more]');
        var less = more ? null : e.target.closest('[data-grid-less]');
        var btn = more || less;
        if (!btn) { return; }

        var wrap = document.getElementById(btn.getAttribute(more ? 'data-grid-more' : 'data-grid-less'));
        if (!wrap) { return; }

        e.preventDefault();
        var current = toInt(wrap.getAttribute('data-tier'), 0);
        setTier(wrap, current + (more ? 1 : -1));
    });

    function init() {
        document.querySelectorAll('.grid-wrap[data-max-tier]').forEach(function (wrap) {
            var fieldId = wrap.getAttribute('data-state-field');
            var field = fieldId ? document.getElementById(fieldId) : null;
            var start = field && field.value !== ''
                ? toInt(field.value, 0)
                : toInt(wrap.getAttribute('data-tier'), 0);
            setTier(wrap, start);
        });
    }

    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', init);
    } else {
        init();
    }

    if (window.Sys && Sys.WebForms && Sys.WebForms.PageRequestManager) {
        Sys.WebForms.PageRequestManager.getInstance().add_endRequest(init);
    }
})();




:root {
    --og-navy: #0a3d6b;          
    --og-navy-tier1: #1d5486;    
    --og-navy-tier2: #29497a;    
    --og-navy-tier3: #3a6599;    
    --og-ink: #1f2933;
    --og-muted: #5f6b7a;
    --og-border: #d9dfe7;
    --og-surface: #ffffff;
    --og-stripe: #f7f9fc;
    --og-hover: #eaf1fb;
    --og-warn-bg: #fff3dc;
    --og-warn-fg: #7a4a00;
    --og-warn-bar: #e8a317;
    --og-off-bg: #f1f3f5;
    --og-off-fg: #6c757d;
    --og-ok-bg: #e3f4ea;
    --og-ok-fg: #1e6b3a;
    --og-radius: .5rem;
    --og-font-size: .8125rem;    /* 13px - dense but readable */
    --og-cell-y: .45rem;
    --og-cell-x: .6rem;
    --og-grid-max-h: calc(100vh - 17rem);
}


.page-shell {
    max-width: 1680px;
    margin: 0 auto;
    padding: 1.25rem 1.5rem 2rem;
}

.page-head {
    display: flex;
    flex-wrap: wrap;
    align-items: baseline;
    gap: .25rem .75rem;
    margin-bottom: 1rem;
}

.page-head h1 {
    margin: 0;
    font-size: 1.5rem;
    font-weight: 600;
    color: var(--og-navy);
}

.page-head .page-sub {
    color: var(--og-muted);
    font-size: .95rem;
}

/* --------------------------------------------------------------------------
   Filter card
   -------------------------------------------------------------------------- */
.filter-card {
    background: var(--og-surface);
    border: 1px solid var(--og-border);
    border-radius: var(--og-radius);
    padding: 1rem 1rem .75rem;
    margin-bottom: 1rem;
}

.filter-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(10.5rem, 1fr));
    gap: .75rem 1rem;
    align-items: end;
}

.filter-card .form-label,
.filter-card label {
    display: block;
    margin-bottom: .25rem;
    font-size: .72rem;
    font-weight: 600;
    letter-spacing: .04em;
    text-transform: uppercase;
    color: var(--og-muted);
}

.filter-actions {
    display: flex;
    gap: .5rem;
    align-items: end;
}

.filter-hint {
    margin-top: .5rem;
    font-size: .75rem;
    color: var(--og-muted);
}

.filter-card.is-dirty {
    border-color: var(--og-warn-bar);
    box-shadow: 0 0 0 .15rem rgba(232, 163, 23, .15);
}

/*Toolbar above the grid -------------------------------------------------------------------------- */
.grid-toolbar {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: space-between;
    gap: .5rem 1rem;
    margin-bottom: .5rem;
}

.grid-toolbar .toolbar-group {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: .5rem;
}

.grid-count {
    font-size: .875rem;
    color: var(--og-muted);
}

.grid-count b {
    color: var(--og-ink);
}

.grid-tiers {
    display: inline-flex;
    align-items: center;
    gap: .25rem;
    font-size: .8125rem;
    color: var(--og-muted);
}

.grid-tiers .tier-label {
    margin-right: .25rem;
    white-space: nowrap;
}

.grid-tiers .tier-label b {
    color: var(--og-ink);
    font-weight: 600;
}

.grid-legend {
    display: flex;
    flex-wrap: wrap;
    gap: .25rem 1rem;
    margin: 0 0 .5rem;
    padding: 0;
    list-style: none;
    font-size: .75rem;
    color: var(--og-muted);
}

.grid-legend li {
    display: inline-flex;
    align-items: center;
    gap: .35rem;
}

.grid-legend .swatch {
    display: inline-block;
    width: .85rem;
    height: .85rem;
    border-radius: .2rem;
    border: 1px solid var(--og-border);
}

.grid-legend .swatch-warn { background: var(--og-warn-bg); box-shadow: inset 3px 0 0 var(--og-warn-bar); }
.grid-legend .swatch-off  { background: var(--og-off-bg); }

/* --------------------------------------------------------------------------
   Scroll container: sticky header + sticky first column live inside it
   -------------------------------------------------------------------------- */
.grid-wrap {
    position: relative;
    overflow: auto;
    max-height: var(--og-grid-max-h);
    background: var(--og-surface);
    border: 1px solid var(--og-border);
    border-radius: var(--og-radius);
}

.grid-wrap.no-max-height { max-height: none; }

/* --------------------------------------------------------------------------
   The table (GridView CssClass="og-grid", GridLines="None")
   -------------------------------------------------------------------------- */
table.og-grid {
    width: 100%;
    margin: 0;
    border-collapse: separate;   /* required for sticky cells to keep borders */
    border-spacing: 0;
    font-size: var(--og-font-size);
    color: var(--og-ink);
}

table.og-grid th,
table.og-grid td {
    padding: var(--og-cell-y) var(--og-cell-x);
    border: 0;
    border-bottom: 1px solid var(--og-border);
    vertical-align: top;
    text-align: left;
}

/* Header - works for both <thead><tr><th> and GridView's default <tr class="og-head"><th> */
table.og-grid > thead > tr > th,
table.og-grid tr.og-head > th {
    position: sticky;
    top: 0;
    z-index: 2;
    background: var(--og-navy);
    color: #fff;
    font-size: .75rem;
    font-weight: 600;
    letter-spacing: .02em;
    line-height: 1.25;
    white-space: nowrap;
    vertical-align: bottom;
    border-bottom: 0;
}

table.og-grid tr.og-head > th a,
table.og-grid > thead > tr > th a {
    color: inherit;
    text-decoration: none;
}

table.og-grid tr.og-head > th a:hover,
table.og-grid > thead > tr > th a:hover {
    text-decoration: underline;
}

/* sort indicators - GridHelper adds sort-asc / sort-desc to the sorted header cell */
table.og-grid th.sort-asc a::after  { content: " \25B2"; font-size: .65em; }
table.og-grid th.sort-desc a::after { content: " \25BC"; font-size: .65em; }

/* tier header shading keeps the old "column group" colour idea */
table.og-grid th.tier-1 { background: var(--og-navy-tier1); }
table.og-grid th.tier-2 { background: var(--og-navy-tier2); }
table.og-grid th.tier-3 { background: var(--og-navy-tier3); }

/* Body */
table.og-grid td { background: var(--og-surface); }
table.og-grid tbody tr:nth-child(even) > td { background: var(--og-stripe); }
table.og-grid tbody tr:hover > td { background: var(--og-hover); }

table.og-grid td a {
    color: var(--og-navy);
    font-weight: 600;
    text-decoration: none;
}

table.og-grid td a:hover { text-decoration: underline; }

/* Sticky first column (the name link) so it stays put while scrolling right */
table.og-grid.sticky-first th:first-child,
table.og-grid.sticky-first td:first-child {
    position: sticky;
    left: 0;
    z-index: 1;
    white-space: nowrap;
    box-shadow: 1px 0 0 var(--og-border);
}

table.og-grid.sticky-first > thead > tr > th:first-child,
table.og-grid.sticky-first tr.og-head > th:first-child {
    z-index: 3;
}

/* Column helpers (ItemStyle-CssClass) */
table.og-grid .col-id     { white-space: nowrap; font-variant-numeric: tabular-nums; }
table.og-grid .col-code   { white-space: nowrap; text-align: center; }
table.og-grid .col-date   { white-space: nowrap; font-variant-numeric: tabular-nums; }
table.og-grid .col-nowrap { white-space: nowrap; }
table.og-grid .col-wrap   { white-space: normal; min-width: 12rem; max-width: 22rem; }
table.og-grid .col-note   { white-space: normal; min-width: 14rem; max-width: 28rem; color: var(--og-muted); }
table.og-grid .col-email  { white-space: nowrap; }

/* --------------------------------------------------------------------------
   Row / cell states (replace the old per-index BackColor calls)
   -------------------------------------------------------------------------- */
/* Row has at least one missing required value: amber bar on the left edge */
table.og-grid tr.row-warn > td:first-child {
    box-shadow: inset 3px 0 0 var(--og-warn-bar), 1px 0 0 var(--og-border);
}

/* The specific empty cell */
table.og-grid td.cell-missing,
table.og-grid tbody tr:nth-child(even) > td.cell-missing,
table.og-grid tbody tr:hover > td.cell-missing {
    background: var(--og-warn-bg);
    color: var(--og-warn-fg);
}

table.og-grid td.cell-missing::after {
    content: "missing";
    font-size: .7rem;
    font-style: italic;
}


table.og-grid tr.row-off > td,
table.og-grid tbody tr.row-off:nth-child(even) > td {
    background: var(--og-off-bg);
    color: var(--og-off-fg);
}

table.og-grid tr.row-off > td a { color: var(--og-off-fg); }

/* Status badges */
.og-badge {
    display: inline-block;
    padding: .1rem .5rem;
    border-radius: 999px;
    font-size: .7rem;
    font-weight: 600;
    line-height: 1.4;
    white-space: nowrap;
}

.og-badge-ok   { background: var(--og-ok-bg);  color: var(--og-ok-fg); }
.og-badge-off  { background: #e2e5e9;          color: #495057; }
.og-badge-warn { background: var(--og-warn-bg); color: var(--og-warn-fg); }


table.og-grid .tier-1,
table.og-grid .tier-2,
table.og-grid .tier-3 {
    display: none;
}

.grid-wrap[data-tier="1"] table.og-grid .tier-1,
.grid-wrap[data-tier="2"] table.og-grid .tier-1,
.grid-wrap[data-tier="2"] table.og-grid .tier-2,
.grid-wrap[data-tier="3"] table.og-grid .tier-1,
.grid-wrap[data-tier="3"] table.og-grid .tier-2,
.grid-wrap[data-tier="3"] table.og-grid .tier-3 {
    display: table-cell;
}

table.og-grid tr.og-pager > td {
    background: var(--og-surface);
    border-bottom: 0;
    padding: .75rem;
}

table.og-grid tr.og-pager table {
    margin: 0 auto;
    border-collapse: separate;
    border-spacing: .25rem 0;
}

table.og-grid tr.og-pager table td {
    padding: 0;
    border: 0;
    background: transparent;
}

table.og-grid tr.og-pager a,
table.og-grid tr.og-pager span {
    display: inline-block;
    min-width: 2rem;
    padding: .3rem .6rem;
    border: 1px solid var(--og-border);
    border-radius: .375rem;
    text-align: center;
    text-decoration: none;
    color: var(--og-navy);
    font-weight: 400;
}

table.og-grid tr.og-pager span {
    background: var(--og-navy);
    border-color: var(--og-navy);
    color: #fff;
}

table.og-grid tr.og-pager a:hover { background: var(--og-hover); }


table.og-grid tr.og-empty > td {
    padding: 2.5rem 1rem;
    text-align: center;
    color: var(--og-muted);
    background: var(--og-surface);
}


.grid-pager {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: space-between;
    gap: .5rem 1rem;
    margin-top: .75rem;
    font-size: .875rem;
    color: var(--og-muted);
}

.grid-pager .pagination {
    margin: 0;
}

.grid-pager .pagination .page-link {
    color: var(--og-navy);
}

.grid-pager .pagination .active .page-link,
.grid-pager .pagination .page-item.active > .page-link {
    background: var(--og-navy);
    border-color: var(--og-navy);
    color: #fff;
}


.grid-wrap.is-compact { --og-cell-y: .25rem; --og-cell-x: .45rem; }

@media (max-width: 768px) {
    .page-shell { padding: .75rem .75rem 1.5rem; }
    .page-head h1 { font-size: 1.25rem; }
    .grid-wrap { --og-grid-max-h: 70vh; }
}

@media print {
    .filter-card,
    .grid-toolbar,
    .grid-legend,
    .grid-pager { display: none !important; }

    .grid-wrap {
        max-height: none;
        overflow: visible;
        border: 0;
    }

    table.og-grid th,
    table.og-grid td {
        position: static !important;
        box-shadow: none !important;
    }

    table.og-grid .tier-1,
    table.og-grid .tier-2,
    table.og-grid .tier-3 { display: table-cell !important; }

    table.og-grid tr.og-head > th,
    table.og-grid > thead > tr > th {
        background: #fff !important;
        color: #000 !important;
        border-bottom: 1px solid #000;
    }
}
-----g
