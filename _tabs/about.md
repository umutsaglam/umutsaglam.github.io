---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---


<div id="badges" class="badges-container"></div>

<script>
    fetch('https://tryhackme.com/api/v2/badges/public-profile?userPublicId=2271198')
        .then(response => {
            if (!response.ok) {
                throw new Error('Ağ yanıtı düzgün değil');
            }
            return response.json();
        })
        .then(data => {
            const badgesDiv = document.getElementById('badges');
            if (data.badges && data.badges.length > 0) {
                data.badges.forEach(badge => {
                    const badgeElement = document.createElement('div');
                    badgeElement.classList.add('badge-item'); // Temaya uygun sınıf ekleme
                    badgeElement.textContent = badge.name; // İstediğiniz bilgiyi ekleyin
                    badgesDiv.appendChild(badgeElement);
                });
            } else {
                badgesDiv.textContent = 'Hiçbir rozet bulunamadı.';
            }
        })
        .catch(error => console.error('Hata:', error));
</script>

<style>
    .badges-container {
        display: flex;
        flex-wrap: wrap;
        gap: 10px; /* Rozetler arasındaki boşluk */
    }

    .badge-item {
        padding: 10px;
        border: 1px solid #ccc; /* Rozet sınırı */
        border-radius: 5px; /* Kenar yuvarlama */
        background-color: #f9f9f9; /* Arka plan rengi */
    }
</style>

z
### certificate

![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/cybrary-cert-ethical-hacking_page-0001.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/THM-KM78V0PLNA.png?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/Cyber_Threat_Management_Badge.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/Ethical_Hacker_Badge.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/THM-ACWNTUM8VV.png?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/THM-VVLGVZGNXK.png?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/thumbnail_skb_sertifika1.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/thumbnail_skb_sertifika2.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/Pentesting101.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/Python101.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/SQL.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/UC-68a3028f-05f0-4f2e-a3ec-3984222bcb2b.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/UC-966ab29d-013b-4afb-bbe5-72c9cd1d31fa.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/UC-8418e744-fd53-48c4-b088-ff7408cf42ef.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/UC-aa4d7780-8279-441b-844c-d4825b4db5a0.jpg?raw=true)
![](https://github.com/umutsaglam/umutsaglam.github.io/blob/main/images/sertifika/UC-ee335d92-41cd-46bf-acbc-0b6240aed35b.jpg?raw=true)




