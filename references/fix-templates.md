# Fix templates

Templates are starting points. Adapt to the project framework. Replace every bracketed placeholder. Have a lawyer review the final text.

## 1. Neutral age gate (HTML + JS)

```html
<label for="birth-year">Year of birth</label>
<select id="birth-year" name="birth_year" required></select>
<script>
  const sel = document.getElementById('birth-year');
  const y = new Date().getFullYear();
  for (let i = y; i >= y - 110; i--) sel.add(new Option(i, i));
  document.querySelector('form').addEventListener('submit', (e) => {
    const age = y - Number(sel.value);
    if (!sel.value || age < 13 || sessionStorage.getItem('age_blocked')) {
      e.preventDefault();
      sessionStorage.setItem('age_blocked', '1');
      alert('Sorry. We cannot create an account for you.');
    }
  });
</script>
```

Server side: validate the age again. Do not store the birth date unless needed. Store only an over-13 boolean.

## 2. Self-host fonts

Next.js: `import { Inter } from 'next/font/google'` (downloads at build time and serves from your domain).
Other stacks: `npm i @fontsource/inter` then `import '@fontsource/inter/400.css'`.
Remove these lines:
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter&display=swap" rel="stylesheet">
```

## 3. Consent-gated analytics loader

```js
function loadReplay() {
  if (navigator.globalPrivacyControl) return;
  if (localStorage.getItem('analytics_consent') !== 'granted') return;
  const s = document.createElement('script');
  s.src = '[VENDOR_SCRIPT_URL]';
  s.async = true;
  document.head.appendChild(s);
}
window.addEventListener('consent-granted', loadReplay);
```

Mask inputs in the vendor config (for example `maskAllInputs: true`). Disable recording on payment and password pages.

## 4. Email footer

```html
<p style="font-size:12px;color:#666">
  You are receiving this because you signed up at [SITE_NAME].<br>
  [COMPANY_LEGAL_NAME] · [POSTAL_ADDRESS]<br>
  <a href="{{unsubscribe_url}}">Unsubscribe</a>
</p>
```

Headers:
```
List-Unsubscribe: <https://[DOMAIN]/unsubscribe?token={{token}}>, <mailto:unsubscribe@[DOMAIN]>
List-Unsubscribe-Post: List-Unsubscribe=One-Click
```

Honour every opt-out within 10 business days and never sell or transfer the address.

## 5. Renewal disclosure next to the pay button

```html
<p class="renewal-terms">
  [PLAN_NAME]: $[PRICE] per [month|year]. Your subscription renews automatically every [period] at $[PRICE] until you cancel.
  [If trial: Your free trial ends on [DATE]. You will be charged $[PRICE] unless you cancel before then.]
  Cancel any time online at Account > Billing.
</p>
<label><input type="checkbox" required> I agree to the automatic renewal terms above.</label>
<button type="submit">Start subscription</button>
```

Also provide: an online cancel button, a confirmation email that restates the terms and cancellation method and an annual reminder for annual plans.

## 6. DMCA policy page outline

- Title: Copyright and DMCA Policy
- How to send a notice of claimed infringement (the six elements in 17 U.S.C. 512(c)(3)(A))
- Designated agent: [NAME or ROLE] · [ADDRESS] · [EMAIL] · [PHONE]
- Counter-notice process (17 U.S.C. 512(g))
- Repeat-infringer policy: accounts are terminated in appropriate circumstances
- Link to this page from the footer and from every upload form

Register the same agent details at https://dmca.copyright.gov ($6 per designation. Renew every three years).
