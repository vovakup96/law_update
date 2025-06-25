const BOT_TOKEN = '7577503200:AAHts3UALDn48OuCbPc6MlaR434waYtFliM';
const CHAT_ID = '361851360';
const LAW_URL = 'https://itd.rada.gov.ua/billinfo/Bills/Card/56499';

async function fetchStatus(): Promise<string | null> {
  const res = await fetch(LAW_URL);
  const html = await res.text();
  const match = html.match(/Стан розгляду:<\/td>\s*<td[^>]*>(.*?)<\/td>/i);
  return match ? match[1].trim() : null;
}

Deno.serve(async () => {
  const currentStatus = await fetchStatus();
  if (!currentStatus) return new Response('Не знайдено', { status: 500 });

  const prev = Deno.env.get('LAST_STATUS');
  if (prev !== currentStatus) {
    await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        chat_id: CHAT_ID,
        text: `📌 №13335 – Статус: ${currentStatus}\n🔗 ${LAW_URL}`,
      }),
    });
    await Deno.env.set('LAST_STATUS', currentStatus);
  }

  return new Response(`OK: ${currentStatus}`);
});
