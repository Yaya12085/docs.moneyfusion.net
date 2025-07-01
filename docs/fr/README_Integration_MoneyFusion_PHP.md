# 💳 Intégration de Money Fusion avec PHP – Vente de Produits Digitaux

> Un exemple d'intégration simple de l'API REST Money Fusion pour automatiser la vente de produits numériques avec PHP + MySQL.

## 🚀 Cas d’usage

Ce projet montre comment :
- Créer dynamiquement un lien de paiement avec l’API Money Fusion
- Gérer les webhooks de confirmation de paiement
- Automatiser la livraison des produits digitaux
- Suivre les statistiques dans un dashboard PHP

---

## 🔧 Technologies utilisées

- 🐘 PHP 8+
- 🐬 MySQL
- 🌐 HTML + Tailwind CSS
- 🔐 cURL (pour appeler l’API Money Fusion)
- 💸 API REST Money Fusion – [https://www.pay.moneyfusion.net](https://www.pay.moneyfusion.net)

---

## 📥 1. Création dynamique d’un lien de paiement

```php
$data = [
    "amount" => 5000,
    "currency" => "XOF",
    "email" => "client@email.com",
    "description" => "Paiement pour Ebook X",
    "callback_url" => "https://mon-site.com/success.php",
    "cancel_url" => "https://mon-site.com/cancel.php"
];

$ch = curl_init("https://www.pay.moneyfusion.net/api/payment");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($data));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    "Authorization: Bearer VOTRE_CLE_API"
]);

$response = curl_exec($ch);
curl_close($ch);

$result = json_decode($response, true);
header("Location: " . $result['payment_url']);
exit;
```

---

## 🔔 2. Réception des webhooks

```php
// webhook.php
$payload = file_get_contents('php://input');
$data = json_decode($payload, true);

if ($data && $data['status'] === 'success') {
    $email = $data['email'];
    $montant = $data['amount'];
    $produit_id = $data['reference'];

    // Connexion DB et insertion
    $stmt = $conn->prepare("INSERT INTO transactions (email_client, produit_id, montant, statut, date_paiement) VALUES (?, ?, ?, 'success', NOW())");
    $stmt->bind_param("sid", $email, $produit_id, $montant);
    $stmt->execute();

    // Envoi automatique (optionnel)
    // mail($email, "Votre produit", "Voici votre lien de téléchargement : ...");
}
```

---

## 📊 3. Dashboard statistiques (extrait)

```php
// Total ventes
$sql = "SELECT COUNT(*) FROM transactions WHERE statut = 'success'";
$total = $conn->query($sql)->fetch_row()[0];

// Revenus
$sql = "SELECT SUM(montant) FROM transactions WHERE statut = 'success'";
$revenus = $conn->query($sql)->fetch_row()[0];

// Clients uniques
$sql = "SELECT COUNT(DISTINCT email_client) FROM transactions WHERE statut = 'success'";
$clients = $conn->query($sql)->fetch_row()[0];
```

---

## 🧠 Conseils utiles

- **Sécurisez vos endpoints webhook** (ex : token secret ou IP autorisée)
- Testez en sandbox avant de passer en production
- Ajoutez une page de **livraison automatique** après paiement
- Enregistrez toutes les transactions dans une base de données

---

## 📎 Crédits

- API Money Fusion – [https://www.pay.moneyfusion.net](https://www.pay.moneyfusion.net)
- Exemple réalisé par [Ton Nom ou pseudo] pour son site de vente de produits digitaux

---

## 🙌 Merci à Money Fusion

API simple, rapide et adaptée au marché africain. Continuez à innover pour faciliter les paiements numériques 👏