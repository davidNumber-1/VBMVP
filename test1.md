
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
