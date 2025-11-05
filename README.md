<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kapsamlı Devre Analizi Rehberi</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        /* Talimatlara uygun olarak, grafik kapları için özel stiller */
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 600px; /* Okunabilirlik için maks. genişlik */
            margin-left: auto;
            margin-right: auto;
            height: 300px; /* Mobil için temel yükseklik */
            max-height: 400px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 350px; /* Daha büyük ekranlarda yükseklik */
            }
        }

        /* Aktif navigasyon bağlantısı stili */
        .nav-link.active {
            background-color: #e0f2fe; /* light-blue-100 */
            color: #0c4a6e; /* blue-900 */
            font-weight: 600;
        }
        /* Basit "açık/kapalı" lamba stili (Mantık Kapıları için) */
        .logic-light {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            border: 2px solid #9ca3af; /* gray-400 */
            transition: background-color 0.3s;
        }
        .logic-light.on {
            background-color: #fde047; /* yellow-300 */
            border-color: #facc15; /* yellow-500 */
            box-shadow: 0 0 15px #facc15;
        }
        .logic-light.off {
            background-color: #374151; /* gray-700 */
            border-color: #1f2937; /* gray-800 */
        }
        /* Basit "switch" stili */
        .logic-switch {
            cursor: pointer;
            padding: 8px 16px;
            border-radius: 8px;
            font-weight: 500;
            user-select: none;
            transition: background-color 0.3s;
        }
        .logic-switch.on {
            background-color: #22c55e; /* green-500 */
            color: white;
        }
        .logic-switch.off {
            background-color: #ef4444; /* red-500 */
            color: white;
        }
        /* Güç Üçgeni için HTML/CSS Stili */
        .power-triangle {
            position: relative;
            width: 300px;
            height: 200px;
            margin: 20px auto;
        }
        .power-triangle-p {
            width: 250px; height: 4px; background: #2563eb; position: absolute; bottom: 0; left: 0;
        }
        .power-triangle-q {
            width: 4px; height: 150px; background: #ef4444; position: absolute; bottom: 0; right: 50px;
        }
        .power-triangle-s {
            width: 292px; height: 4px; background: #16a34a; position: absolute; bottom: 75px; left: -21px;
            transform: rotate(-31deg); transform-origin: bottom left;
        }
    </style>
</head>
<body class="bg-gray-50 font-sans leading-relaxed text-gray-800">

    <!-- 
    Chosen Palette: Calm Neutrals with Blue Accent
    Uygulama Yapı Planı: Yeni teknik içerik talebi üzerine, SPA'nın bilgi mimarisi tamamen yeniden tasarlandı. Basit "Bölüm 0-8" yapısı, bir mühendislik ders müfredatını yansıtacak şekilde 9 mantıksal modüle (Temel Kavramlar, DC Yasaları, DC Analiz, Teoremler, AC Temelleri, Empedans, Transient, İletişim/Dijital) dönüştürüldü. Kalıcı kenar çubuğu navigasyonu korundu ancak bu yeni, daha derin yapıyı yansıtacak şekilde güncellendi. Her bölüm artık daha teknik bilgiler, formüller (örn: Z_L = jwL) ve ilgili matematik temellerini (Karmaşık Sayılar, Diferansiyel Denklemler) içeriyor.
    Görselleştirme ve İçerik Seçenekleri: 
    - Birim Dönüşümler (Bölüm 0) -> Hedef: Temel beceriyi pekiştirme -> Yöntem: İnteraktif Birim Dönüştürücü (JS). Etkileşim: Değer gir, birim seç, dönüştür. Gerekçe: Mühendislik hesaplamaları için kritik bir araç.
    - Karmaşık Sayılar (Bölüm 5) -> Hedef: AC Analiz için matematik temeli sağlama -> Yöntem: İnteraktif Kartezyen/Kutup Dönüştürücü (JS). Etkileşim: a+jb gir -> A∠θ al (ve tersi). Gerekçe: Fazör analizinin temelini oluşturur.
    - Empedans (Bölüm 6) -> Hedef: AC'de R, L, C davranışını hesaplama -> Yöntem: İnteraktif Empedans Hesaplayıcı (JS). Etkileşim: f, L/C gir -> Z_L/Z_C hesapla. Gerekçe: $V=IZ$ formülünü uygulanabilir kılar.
    - AC Güç (Bölüm 6) -> Hedef: P, Q, S ilişkisini gösterme -> Yöntem: HTML/CSS Güç Üçgeni. Gerekçe: Soyut güç kavramlarını görselleştirir.
    - RLC Sönüm (Bölüm 7) -> Hedef: 2. mertebe tepkileri gösterme -> Yöntem: Chart.js Çizgi Grafiği (Canvas). Viz: Aşırı, Kritik, Eksik Sönümlü eğrileri gösterir. Gerekçe: Diferansiyel denklemlerin görsel sonucunu sağlar.
    - Diğer interaktifler (Ohm, VD, Logic) korunmuş ve ilgili yeni bölümlere entegre edilmiştir.
    ONAY: NO SVG graphics used. NO Mermaid JS used.
    -->

    <!-- Header -->
    <header class="bg-white shadow-md">
        <div class="container mx-auto px-4 py-5">
            <h1 class="text-3xl font-bold text-blue-800">Kapsamlı Devre Analizi Rehberi</h1>
            <p class="text-md text-gray-600">Temel kavramlardan ileri seviye AC ve Transient analizlerine, tam teknik bir yolculuk.</p>
        </div>
    </header>

    <!-- Ana İçerik Alanı -->
    <div class="flex flex-col md:flex-row container mx-auto p-4 gap-6">

        <!-- Kenar Çubuğu Navigasyonu (Yeniden Yapılandırıldı) -->
        <nav class="w-full md:w-1/4 lg:w-1/5 bg-white p-4 rounded-lg shadow-md self-start top-4 sticky">
            <h2 class="text-xl font-semibold mb-4 text-blue-900">Modüller</h2>
            <ul class="space-y-2">
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-0">0. Temel Kavramlar & Birimler</a></li>
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-1">1. DC Devre Yasaları</a></li>
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-2">2. DC Analiz Teknikleri</a></li>
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-3">3. Devre Teoremleri</a></li>
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-4">4. Enerji Depolayan Elemanlar</a></li>
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-5">5. AC Analizi: Temeller</a></li>
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-6">6. AC'de Empedans ve Güç</a></li>
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-7">7. Geçici Durum (Transient) Analizi</a></li>
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-8">8. İletişim & Dijital Elektronik</a></li>
                <li><a href="#" class="nav-link block p-3 rounded-lg hover:bg-gray-100" data-target="bolum-9">9. Başarının Anahtarı</a></li>
            </ul>
        </nav>

        <!-- İçerik Alanı (Yeniden Yapılandırıldı) -->
        <main class="w-full md:w-3/4 lg:w-4/5 bg-white p-6 rounded-lg shadow-md">

            <!-- Bölüm 0: Temel Kavramlar & Birimler -->
            <section id="bolum-0" class="bölüm-content space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">0. Temel Kavramlar & Birimler</h2>
                <p class="text-lg">Bu modül, devre analizinin dilini ve temel yapı taşlarını tanıtır. Tüm ileri seviye konular bu temeller üzerine inşa edilir.</p>
                
                <h3 class="text-xl font-semibold text-blue-800">Temel Tanımlar</h3>
                <div class="space-y-2">
                    <p><strong>Elektrik:</strong> Elektronların hareketiyle ortaya çıkan temel bir enerji biçimi.</p>
                    <p><strong>Elektronik:</strong> Bu elektron akışını (bilgi işlemek, sinyal iletmek vb. için) kontrol etme bilimi.</p>
                    <p><strong>Devre:</strong> Akımın akması için kapalı bir yol sağlayan bileşenler (kaynak, direnç, kablo) topluluğu.</p>
                    <p><strong>Devre Analizi:</strong> Bir devredeki gerilim ve akım gibi büyüklükleri matematiksel olarak belirleme süreci.</p>
                </div>

                <h3 class="text-xl font-semibold text-blue-800">Temel Büyüklükler</h3>
                <div class="overflow-x-auto">
                    <table class="w-full min-w-max text-left border-collapse">
                        <thead>
                            <tr class="bg-gray-100">
                                <th class="border p-2">Büyüklük</th>
                                <th class="border p-2">Sembol</th>
                                <th class="border p-2">Birim</th>
                                <th class="border p-2">Su Analojisi</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td class="border p-2">Gerilim (Voltaj)</td>
                                <td class="border p-2">V</td>
                                <td class="border p-2">Volt (V)</td>
                                <td class="border p-2">Su Basıncı (Tazyik)</td>
                            </tr>
                            <tr>
                                <td class="border p-2">Akım</td>
                                <td class="border p-2">I</td>
                                <td class="border p-2">Amper (A)</td>
                                <td class="border p-2">Su Akışı (Debi)</td>
                            </tr>
                            <tr>
                                <td class="border p-2">Direnç</td>
                                <td class="border p-2">R</td>
                                <td class="border p-2">Ohm (Ω)</td>
                                <td class="border p-2">Borudaki Daralma</td>
                            </tr>
                            <tr>
                                <td class="border p-2">Güç</td>
                                <td class="border p-2">P</td>
                                <td class="border p-2">Watt (W)</td>
                                <td class="border p-2">Çarkı Döndürme Hızı (Basınç x Debi)</td>
                            </tr>
                            <tr>
                                <td class="border p-2">Enerji</td>
                                <td class="border p-2">W</td>
                                <td class="border p-2">Joule (J)</td>
                                <td class="border p-2">Toplam Dönen Çark Miktarı (Güç x Zaman)</td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <h3 class="text-xl font-semibold text-blue-800">Birim Dönüşümleri (SI Önekleri)</h3>
                <p>Elektronikte çok büyük (Giga, Mega, Kilo) ve çok küçük (mili, mikro, nano, piko) değerlerle çalışırız. Hızlı dönüşüm yapabilmek kritiktir.</p>
                <!-- İnteraktif Birim Dönüştürücü -->
                <div class="bg-gray-100 p-6 rounded-lg border">
                    <h4 class="text-lg font-semibold mb-4">İnteraktif Birim Dönüştürücü</h4>
                    <div class="flex flex-wrap gap-4 items-end">
                        <div class="flex-1 min-w-[150px]">
                            <label for="unit-value" class="block text-sm font-medium">Değer</label>
                            <input type="number" id="unit-value" value="1" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm">
                        </div>
                        <div class="flex-1 min-w-[120px]">
                            <label for="unit-from" class="block text-sm font-medium">Başlangıç Birimi</label>
                            <select id="unit-from" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm">
                                <option value="1e9">Giga (G)</option>
                                <option value="1e6">Mega (M)</option>
                                <option value="1e3">Kilo (k)</option>
                                <option value="1" selected>Temel Birim (V, A, Ω, W)</option>
                                <option value="1e-3">mili (m)</option>
                                <option value="1e-6">mikro (µ)</option>
                                <option value="1e-9">nano (n)</option>
                                <option value="1e-12">piko (p)</option>
                            </select>
                        </div>
                        <div class="flex-1 min-w-[120px]">
                            <label for="unit-to" class="block text-sm font-medium">Hedef Birim</label>
                            <select id="unit-to" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm">
                                <option value="1e9">Giga (G)</option>
                                <option value="1e6">Mega (M)</option>
                                <option value="1e3">Kilo (k)</option>
                                <option value="1">Temel Birim (V, A, Ω, W)</option>
                                <option value="1e-3" selected>mili (m)</option>
                                <option value="1e-6">mikro (µ)</option>
                                <option value="1e-9">nano (n)</option>
                                <option value="1e-12">piko (p)</option>
                            </select>
                        </div>
                    </div>
                    <p id="unit-result" class="mt-4 text-lg font-medium text-blue-700">1 Temel Birim = 1000.00 mili (m)</p>
                </div>
            </section>

            <!-- Bölüm 1: DC Devre Yasaları -->
            <section id="bolum-1" class="bölüm-content hidden space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">1. DC Devre Yasaları (DC Analiz Temelleri)</h2>
                <p class="text-lg">Doğru Akım (DC) devreleri, gerilimin sabit olduğu (piller gibi) devrelerdir. Bu modül, bu devreleri yöneten temel kuralları içerir.</p>

                <h3 class="text-xl font-semibold text-blue-800">Ohm Kanunu: V = I &middot; R</h3>
                <p>Bir direnç üzerindeki gerilim, akım ve direncin çarpımıdır. Bu, DC analizinin temel taşıdır.</p>
                <!-- İnteraktif Ohm Kanunu Hesaplayıcı -->
                <div class="bg-gray-100 p-6 rounded-lg border">
                    <h4 class="text-lg font-semibold mb-4">İnteraktif Ohm Kanunu Hesaplayıcı</h4>
                    <div class="flex flex-wrap gap-4 items-center">
                        <div class="flex-1 min-w-[150px]"><label for="ohm-v" class="block text-sm font-medium">Gerilim (V)</label><input type="number" id="ohm-v" placeholder="Volt" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"></div>
                        <span class="text-2xl font-bold text-gray-500">=</span>
                        <div class="flex-1 min-w-[150px]"><label for="ohm-i" class="block text-sm font-medium">Akım (I)</label><input type="number" id="ohm-i" placeholder="Amper" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"></div>
                        <span class="text-2xl font-bold text-gray-500">x</span>
                        <div class="flex-1 min-w-[150px]"><label for="ohm-r" class="block text-sm font-medium">Direnç (R)</label><input type="number" id="ohm-r" placeholder="Ohm" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"></div>
                    </div>
                    <div class="mt-4 flex gap-4"><button id="calc-ohm" class="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700">Hesapla</button><button id="reset-ohm" class="bg-gray-300 text-gray-800 px-4 py-2 rounded-lg hover:bg-gray-400">Sıfırla</button></div>
                    <p id="ohm-result" class="mt-4 text-lg font-medium text-blue-700"></p>
                </div>
                
                <h3 class="text-xl font-semibold text-blue-800 mt-4">Kirchhoff Kanunları</h3>
                <ul class="list-disc list-inside space-y-2">
                    <li><strong>Kirchhoff Akım Yasası (KAY/KCL):</strong> &Sigma;I<sub>giren</sub> = &Sigma;I<sub>çıkan</sub>. Bir düğümde akım kaybolmaz. Düğüm analizinin temelidir.</li>
                    <li><strong>Kirchhoff Gerilim Yasası (KGY/KVL):</strong> &Sigma;V<sub>kaynak</sub> = &Sigma;V<sub>düşüş</sub>. Kapalı bir çevrede gerilimlerin cebirsel toplamı sıfırdır. Göz analizinin temelidir.</li>
                </ul>

                <h3 class="text-xl font-semibold text-blue-800">Seri ve Paralel Bağlantılar</h3>
                <p><strong>Seri:</strong> Elemanlar uç uca eklenir. Akım tüm elemanlarda aynıdır. Toplam direnç artar: R<sub>eş</sub> = R<sub>1</sub> + R<sub>2</sub> + ...</p>
                <p><strong>Paralel:</strong> Elemanların uçları birbirine bağlanır. Gerilim tüm elemanlarda aynıdır. Toplam direnç azalır: 1/R<sub>eş</sub> = 1/R<sub>1</sub> + 1/R<sub>2</sub> + ...</p>

                <h3 class="text-xl font-semibold text-blue-800">Gerilim ve Akım Bölücüler (Kısayollar)</h3>
                <p>Bu kurallar, basit seri/paralel yapılarda hızlı hesaplama sağlar.</p>
                <!-- İnteraktif Gerilim Bölücü Hesaplayıcı -->
                <div class="bg-gray-100 p-6 rounded-lg border">
                    <h4 class="text-lg font-semibold mb-4">İnteraktif Gerilim Bölücü Hesaplayıcı</h4>
                    <p class="mb-2">Seri devrede, R2 üzerindeki gerilimi hesaplar: V<sub>out</sub> = V<sub>kaynak</sub> &times; (R2 / (R1 + R2))</p>
                    <div class="flex flex-wrap gap-4 items-end">
                        <div class="flex-1 min-w-[120px]"><label for="vd-vin" class="block text-sm font-medium">V-Kaynak (Volt)</label><input type="number" id="vd-vin" value="12" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm"></div>
                        <div class="flex-1 min-w-[120px]"><label for="vd-r1" class="block text-sm font-medium">R1 (Ohm)</label><input type="number" id="vd-r1" value="1000" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm"></div>
                        <div class="flex-1 min-w-[120px]"><label for="vd-r2" class="block text-sm font-medium">R2 (Ohm)</label><input type="number" id="vd-r2" value="1000" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm"></div>
                        <button id="calc-vd" class="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 h-[42px]">Hesapla</button>
                    </div>
                    <p id="vd-result" class="mt-4 text-xl font-semibold text-blue-700">Çıkış Gerilimi (V-out): 6.00 V</p>
                </div>
                <p class="mt-2"><strong>Akım Bölücü:</strong> Paralel devrede, R1 kolundan geçen akımı hesaplar: I<sub>1</sub> = I<sub>toplam</sub> &times; (R<sub>2</sub> / (R<sub>1</sub> + R<sub>2</sub>)) (Akım, düşük dirence daha çok gittiği için formül terstir).</p>
            </section>

            <!-- Bölüm 2: DC Analiz Teknikleri -->
            <section id="bolum-2" class="bölüm-content hidden space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">2. DC Analiz Teknikleri (Sistematik Çözüm)</h2>
                <p class="text-lg">Daha karmaşık devreler için sistematik yöntemler gerekir. Bu yöntemler devreyi, matris kullanarak çözülebilen doğrusal denklem sistemlerine dönüştürür.</p>
                
                <h3 class="text-xl font-semibold text-blue-800">Düğüm Gerilimleri Yöntemi (Nodal Analysis)</h3>
                <p>En güçlü ve yaygın yöntemdir. KCL (Kirchhoff Akım Yasası) temellidir.</p>
                <ol class="list-decimal list-inside space-y-1">
                    <li>Bir referans düğüm (genellikle toprak, 0V) seçilir.</li>
                    <li>Diğer ana düğümlere bilinmeyen gerilimler (V<sub>1</sub>, V<sub>2</sub>, ...) atanır.</li>
                    <li>Her bilinmeyen düğüm için KCL denklemi yazılır (Genellikle &Sigma; ((V<sub>düğüm</sub> - V<sub>komşu</sub>) / R) = 0 şeklinde).</li>
                    <li>Elde edilen N-1 adet denklem (N=düğüm sayısı) matris kullanılarak çözülür.</li>
                </ol>

                <h3 class="text-xl font-semibold text-blue-800">Göz (Çevre) Akımları Yöntemi (Mesh Analysis)</h3>
                <p>KVL (Kirchhoff Gerilim Yasası) temellidir. Özellikle "düzlemsel" (üst üste binmeyen) devrelerde kullanışlıdır.</p>
                <ol class="list-decimal list-inside space-y-1">
                    <li>Devredeki her kapalı "göze" hayali bir çevre akımı (I<sub>1</sub>, I<sub>2</sub>, ...) atanır.</li>
                    <li>Her çevre için KVL denklemi yazılır (&Sigma;V<sub>kaynak</sub> = &Sigma;I &middot; R).</li>
                    <li>Elemanlar birden fazla göze aitse, akımların farkı alınır (örn: (I<sub>1</sub> - I<sub>2</sub>) &middot; R).</li>
                    <li>Elde edilen denklem sistemi çözülür.</li>
                </ol>

                <h3 class="text-xl font-semibold text-blue-800">Süperpozisyon Teoremi</h3>
                <p>Devrede birden fazla <strong>bağımsız</strong> kaynak varsa kullanılır.</p>
                <ol class="list-decimal list-inside space-y-1">
                    <li>Her seferinde bir kaynak devrede tutulur, diğerleri "kapatılır" (Gerilim kaynakları kısa devre, akım kaynakları açık devre).</li>
                    <li>İlgilenilen akım/gerilim, her kaynak için ayrı ayrı hesaplanır.</li>
                    <li>Sonuç, tüm bu ayrı hesaplamaların cebirsel toplamıdır. (Yönlere dikkat!).</li>
                </ol>
            </section>

            <!-- Bölüm 3: Devre Teoremleri -->
            <section id="bolum-3" class="bölüm-content hidden space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">3. Devre Teoremleri (Basitleştirme)</h2>
                <p class="text-lg">Bu teoremler, devrenin tamamını çözmek yerine, sadece ilgilendiğimiz iki nokta (örn: bir yükün bağlandığı A-B uçları) arasındaki davranışı analiz etmek için kullanılır.</p>

                <h3 class="text-xl font-semibold text-blue-800">Thevenin Teoremi</h3>
                <p>En güçlü basitleştirme teoremidir. Bir "yük" açısından bakıldığında, ne kadar karmaşık olursa olsun devrenin geri kalanının tamamı, <strong>tek bir gerilim kaynağı (V<sub>th</sub>)</strong> ve ona <strong>seri bağlı tek bir direnç (R<sub>th</sub>)</strong> olarak modellenebilir.</p>
                <ul class="list-disc list-inside ml-4">
                    <li><strong>V<sub>th</sub> (Thevenin Gerilimi):</strong> A-B uçları "açık devre" iken ölçülen gerilim.</li>
                    <li><strong>R<sub>th</sub> (Thevenin Direnci):</strong> A-B uçlarından içeri bakıldığında görülen eşdeğer direnç (Tüm bağımsız kaynaklar kapatılmışken).</li>
                </ul>

                <h3 class="text-xl font-semibold text-blue-800">Norton Teoremi</h3>
                <p>Thevenin'in "dual"idir (ikizi). Karmaşık devreyi, <strong>tek bir akım kaynağı (I<sub>N</sub>)</strong> ve ona <strong>paralel bağlı tek bir direnç (R<sub>N</sub>)</strong> olarak modeller.</p>
                <ul class="list-disc list-inside ml-4">
                    <li><strong>I<sub>N</sub> (Norton Akımı):</strong> A-B uçları "kısa devre" yapıldığında akan akım.</li>
                    <li><strong>R<sub>N</sub> (Norton Direnci):</strong> R<sub>th</sub> ile aynıdır (R<sub>N</sub> = R<sub>th</sub>).</li>
                    <li>Dönüşüm: V<sub>th</sub> = I<sub>N</sub> &middot; R<sub>th</sub></li>
                </ul>

                <h3 class="text-xl font-semibold text-blue-800">Maksimum Güç Transferi</h3>
                <p>Bir kaynaktan (Thevenin eşdeğeri V<sub>th</sub>, R<sub>th</sub> olan) bir yüke (R<sub>L</sub>) maksimum gücü aktarmak için, <strong>Yük Direnci, Kaynağın Thevenin Direncine eşit olmalıdır (R<sub>L</sub> = R<sub>th</sub>)</strong>. Bu durumda yüke aktarılan maksimum güç P<sub>max</sub> = V<sub>th</sub><sup>2</sup> / (4 &middot; R<sub>th</sub>) olur.</p>
            </section>
            
            <!-- Bölüm 4: Enerji Depolayan Elemanlar -->
            <section id="bolum-4" class="bölüm-content hidden space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">4. Enerji Depolayan Elemanlar</h2>
                <p class="text-lg">Direnç (R) enerjiyi harcar (ısıya çevirir). Kapasitör (C) ve Bobin (L) ise enerjiyi depolar ve devreye geri verebilir. Bu elemanların davranışı zamana bağlıdır.</p>
                
                <div class="grid md:grid-cols-2 gap-4">
                    <div class="bg-blue-50 p-4 rounded-lg border border-blue-200">
                        <h3 class="font-semibold text-xl text-blue-800">Kapasitör (C) - Kondansatör</h3>
                        <p>Enerjiyi "Elektrik Alan" olarak (Gerilim şeklinde) depolar. (Su kulesi gibi).</p>
                        <p class="font-mono mt-2">i(t) = C &middot; (dv(t)/dt)</p>
                        <p class="mt-2"><strong>Temel Kuralı:</strong> Üzerindeki <strong>gerilimin</strong> ani değişmesine izin vermez. DC'de (sürekli durumda) "açık devre" gibi davranır.</p>
                    </div>
                    <div class="bg-green-50 p-4 rounded-lg border border-green-200">
                        <h3 class="font-semibold text-xl text-green-800">Bobin (L) - İndüktör</h3>
                        <p>Enerjiyi "Manyetik Alan" olarak (Akım şeklinde) depolar. (Ağır su türbini gibi).</p>
                        <p class="font-mono mt-2">v(t) = L &middot; (di(t)/dt)</p>
                        <p class="mt-2"><strong>Temel Kuralı:</strong> İçinden geçen <strong>akımın</strong> ani değişmesine izin vermez. DC'de (sürekli durumda) "kısa devre" gibi davranır.</p>
                    </div>
                </div>
            </section>
            
            <!-- Bölüm 5: AC Analizi: Temeller -->
            <section id="bolum-5" class="bölüm-content hidden space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">5. AC Analizi: Temeller (Sinüsoidal Analiz)</h2>
                <p class="text-lg">AC (Alternatif Akım), gerilim ve akımın sinüs dalgası şeklinde sürekli değiştiği durumlardır. Bu analiz, zamanla değişen ($v(t)$) sinyalleri, cebirsel olarak çözülebilen "Fazör"lere dönüştürme üzerine kuruludur.</p>

                <!-- AC Sinyal Grafiği -->
                <div class="bg-gray-100 p-6 rounded-lg border">
                    <h4 class="text-lg font-semibold mb-4">Sinüzoidal AC Sinyali</h4>
                    <p>Genlik (V<sub>m</sub>), Frekans (f veya &omega;) ve Faz (&phi;) ile tanımlanır: v(t) = V<sub>m</sub> cos(&omega;t + &phi;)</p>
                    <div class="chart-container">
                        <canvas id="acChart"></canvas>
                    </div>
                </div>

                <h3 class="text-xl font-semibold text-blue-800">Matematik Temeli: Karmaşık (Kompleks) Sayılar</h3>
                <p>AC analizi, diferansiyel denklemler yerine karmaşık sayılarla cebirsel işlem yapmayı sağlar. j = &radic;-1 operatörünü kullanırız.</p>
                <ul class="list-disc list-inside ml-4">
                    <li><strong>Kartezyen Form:</strong> Z = a + jb (Reel + İmajiner)</li>
                    <li><strong>Kutupsal (Polar) Form:</strong> Z = A &ang; &phi; (Genlik + Faz Açısı)</li>
                </ul>
                <!-- İnteraktif Karmaşık Sayı Dönüştürücü -->
                <div class="bg-gray-100 p-6 rounded-lg border">
                    <h4 class="text-lg font-semibold mb-4">İnteraktif Fazör Dönüştürücü (a+jb ↔ A∠θ)</h4>
                    <div class="flex flex-wrap gap-4 items-center">
                        <input type="number" id="complex-a" placeholder="Reel (a)" class="flex-1 min-w-[100px] px-3 py-2 border rounded-md">
                        <span class="font-bold">+ j</span>
                        <input type="number" id="complex-b" placeholder="İmajiner (b)" class="flex-1 min-w-[100px] px-3 py-2 border rounded-md">
                        <button id="to-polar" class="bg-blue-600 text-white px-3 py-2 rounded-lg">→ Kutupsal</button>
                    </div>
                    <div class="flex flex-wrap gap-4 items-center mt-4">
                        <input type="number" id="complex-A" placeholder="Genlik (A)" class="flex-1 min-w-[100px] px-3 py-2 border rounded-md">
                        <span class="font-bold">∠</span>
                        <input type="number" id="complex-phi" placeholder="Açı (θ) °" class="flex-1 min-w-[100px] px-3 py-2 border rounded-md">
                        <button id="to-cartesian" class="bg-blue-600 text-white px-3 py-2 rounded-lg">→ Kartezyen</button>
                    </div>
                    <p id="complex-result" class="mt-4 text-lg font-medium text-blue-700"></p>
                </div>

                <h3 class="text-xl font-semibold text-blue-800">Fazörler (Phasors)</h3>
                <p>Fazör, sinüsoidal bir sinyali, onun genlik ve faz açısını kullanan bir karmaşık sayıya (vektöre) dönüştürme yöntemidir.</p>
                <p class="font-mono text-center bg-gray-100 p-2 rounded-md">v(t) = V<sub>m</sub> cos(&omega;t + &phi;) &nbsp; &hArr; &nbsp; <b>V</b> = V<sub>eff</sub> &ang; &phi;</p>
                <p>(Not: Mühendislikte genlik için genellikle V<sub>m</sub> yerine Etkin Değer (V<sub>eff</sub> = V<sub>m</sub> / &radic;2) kullanılır).</p>
            </section>

            <!-- Bölüm 6: AC'de Empedans ve Güç -->
            <section id="bolum-6" class="bölüm-content hidden space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">6. AC'de Empedans (Z) ve Güç</h2>
                <p class="text-lg">Fazör dünyasında, direncin genelleştirilmiş haline <strong>Empedans (Z)</strong> denir. Ohm Kanunu, V = I &middot; Z olarak güncellenir. Z, hem direnç (reel kısım) hem de "Reaktans" (imajiner kısım) içerir.</p>

                <h3 class="text-xl font-semibold text-blue-800">Devre Elemanlarının Empedansları</h3>
                <ul class="list-disc list-inside space-y-2">
                    <li><strong>Direnç (R):</strong> Z<sub>R</sub> = R (Açı 0&deg;, akım ve gerilim aynı fazda)</li>
                    <li><strong>Bobin (L):</strong> Z<sub>L</sub> = j&omega;L = (&omega;L) &ang; 90&deg; (Gerilim, akımdan 90&deg; ileride)</li>
                    <li><strong>Kapasitör (C):</strong> Z<sub>C</sub> = 1 / (j&omega;C) = -j / (&omega;C) = (1 / (&omega;C)) &ang; -90&deg; (Gerilim, akımdan 90&deg; geride)</li>
                </ul>
                <p><strong>Admitans (Y)</strong>, empedansın tersidir: Y = 1/Z = G + jB (İletkenlik + Süseptans).</p>

                <!-- İnteraktif Empedans Hesaplayıcı -->
                <div class="bg-gray-100 p-6 rounded-lg border">
                    <h4 class="text-lg font-semibold mb-4">İnteraktif Reaktans/Empedans Hesaplayıcı</h4>
                    <div class="flex flex-wrap gap-4 items-end">
                        <div class="flex-1 min-w-[120px]"><label for="imp-f" class="block text-sm font-medium">Frekans (f) - Hz</label><input type="number" id="imp-f" value="50" class="mt-1 block w-full px-3 py-2 border rounded-md"></div>
                        <div class="flex-1 min-w-[120px]"><label for="imp-l" class="block text-sm font-medium">Bobin (L) - miliHenry (mH)</label><input type="number" id="imp-l" value="10" class="mt-1 block w-full px-3 py-2 border rounded-md"></div>
                        <div class="flex-1 min-w-[120px]"><label for="imp-c" class="block text-sm font-medium">Kapasitör (C) - mikroFarad (µF)</label><input type="number" id="imp-c" value="10" class="mt-1 block w-full px-3 py-2 border rounded-md"></div>
                    </div>
                    <button id="calc-imp" class="mt-4 bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700">Hesapla</button>
                    <p id="imp-result" class="mt-4 text-lg font-medium text-blue-700"></p>
                </div>
                
                <h3 class="text-xl font-semibold text-blue-800">AC Güç (Güç Üçgeni)</h3>
                <p>AC'de güç, karmaşık bir sayıdır (S = P + jQ):</p>
                <div class="grid md:grid-cols-2 gap-4">
                    <div>
                        <ul class="list-disc list-inside space-y-2">
                            <li><strong>Aktif Güç (P):</strong> Dirençte harcanan "gerçek" iş yapan güç. Birimi: <strong>Watt (W)</strong>.</li>
                            <li><strong>Reaktif Güç (Q):</strong> Bobin ve kapasitörde depolanan, iş yapmayan "sanal" güç. Birimi: <strong>VAR</strong>.</li>
                            <li><strong>Görünür Güç (S):</strong> Kaynağın sağladığı toplam güç (|S| = &radic;(P<sup>2</sup> + Q<sup>2</sup>)). Birimi: <strong>Volt-Amper (VA)</strong>.</li>
                            <li><strong>Güç Faktörü (PF):</strong> PF = cos(&phi;) = P / |S|. Gücün ne kadar verimli kullanıldığını gösterir (1 idealdir).</li>
                        </ul>
                    </div>
                    <div class="bg-gray-50 p-4 rounded-lg flex items-center justify-center">
                        <div class="power-triangle">
                            <div class="power-triangle-p"><span class="absolute -bottom-6 left-1/2 -translate-x-1/2">P (Aktif Güç)</span></div>
                            <div class="power-triangle-q"><span class="absolute top-1/2 -right-12 -translate-y-1/2 rotate-90">Q (Reaktif Güç)</span></div>
                            <div class="power-triangle-s"><span class="absolute top-0 left-1/2 -translate-x-1/2 -translate-y-4">S (Görünür Güç)</span></div>
                        </div>
                    </div>
                </div>
            </section>
            
            <!-- Bölüm 7: Geçici Durum (Transient) Analizi -->
            <section id="bolum-7" class="bölüm-content hidden space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">7. Geçici Durum (Transient) Analizi</h2>
                <p class="text-lg">Bir devrede anahtarın açılması/kapanması gibi ani bir değişiklik (step response) olduğunda, devrenin "yeni" sürekli duruma gelene kadar geçirdiği kısa süreli tepkidir. Bu analiz, diferansiyel denklemlerle çözülür.</p>

                <h3 class="text-xl font-semibold text-blue-800">1. Mertebe Devreler (RC ve RL)</h3>
                <p>Çözümü x(t) = x(&infin;) + [x(0<sup>+</sup>) - x(&infin;)]e<sup>-t/&tau;</sup> formülüne dayanan 1. mertebeden diferansiyel denklemlerle modellenir.</p>
                <ul class="list-disc list-inside ml-4">
                    <li><strong>Zaman Sabitesi (&tau; - Tau):</strong> Devrenin tepki hızını belirler. (Pratikte 5&tau; süresinde sürekli duruma ulaşılır).</li>
                    <li>&tau; = R &middot; C (RC devreleri için)</li>
                    <li>&tau; = L / R (RL devreleri için)</li>
                </ul>
                <div class="bg-gray-100 p-6 rounded-lg border">
                    <h4 class="text-lg font-semibold mb-4">RC Devresi Şarj Grafiği (Doğal Tepki)</h4>
                    <p>Kondansatör geriliminin e<sup>-t/&tau;</sup> eğrisi ile sönümlenmesi:</p>
                    <div class="chart-container">
                        <canvas id="rcChart"></canvas>
                    </div>
                </div>

                <h3 class="text-xl font-semibold text-blue-800">2. Mertebe Devreler (RLC)</h3>
                <p>Devrede R, L ve C'nin bir arada bulunduğu, 2. mertebeden diferansiyel denklemlerle modellenen devrelerdir. Tepkileri "sönüm" (damping) karakteristiğine bağlıdır.</p>
                <ul class="list-disc list-inside ml-4">
                    <li><strong>Aşırı Sönümlü (Overdamped):</strong> Yavaşça, salınım yapmadan sürekli duruma ulaşır.</li>
                    <li><strong>Kritik Sönümlü (Critically Damped):</strong> Mümkün olan en hızlı şekilde, salınım yapmadan sürekli duruma ulaşır.</li>
                    <li><strong>Eksik Sönümlü (Underdamped):</strong> Sürekli duruma ulaşmadan önce bir süre salınım yapar (zil çınlaması gibi).</li>
                </ul>
                <div class="bg-gray-100 p-6 rounded-lg border">
                    <h4 class="text-lg font-semibold mb-4">RLC Devresi Sönüm Tipleri Grafiği</h4>
                    <div class="chart-container">
                        <canvas id="rlcChart"></canvas>
                    </div>
                </div>

                <h3 class="text-xl font-semibold text-blue-800">İleri Teknik: Laplace Dönüşümü</h3>
                <p>Geçici durum analizinin "kestirme" yoludur. Zaman domenindeki ($t$) diferansiyel denklemleri, "s" domenindeki cebirsel denklemlere dönüştürür. Çözüm yapılır ve "Ters Laplace" ile tekrar zaman domenine dönülür. Bu, karmaşık analizleri büyük ölçüde basitleştirir.</p>
            </section>
            
            <!-- Bölüm 8: İletişim & Dijital Elektronik -->
            <section id="bolum-8" class="bölüm-content hidden space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">8. İletişim & Dijital Elektronik</h2>
                <p class="text-lg">Devre analizinin son konuları, sinyallerin nasıl taşındığı (iletişim) ve nasıl "ya hep ya hiç" mantığıyla işlendiği (dijital) üzerine yoğunlaşır.</p>

                <h3 class="text-xl font-semibold text-blue-800">Elektronikte Haberleşme</h3>
                <p>Bilgi (ses, görüntü, veri), "taşıyıcı" adı verilen yüksek frekanslı bir sinyal üzerine "bindirilerek" (modülasyon) iletilir. Radyo, TV, Wi-Fi, 5G hep bu prensibe dayanır. Frekans, elektroniğin "bilgi taşıma kapasitesini" ve "işlem hızını" belirler (Bkz. Bölüm 5).</p>

                <h3 class="text-xl font-semibold text-blue-800">Dijital Elektronik ve Mantık Kapıları</h3>
                <p>Analog elektroniğin aksine (sonsuz ara değer), Dijital Elektronik sadece iki durumla çalışır: <strong>0 (KAPALI, 0V)</strong> ve <strong>1 (AÇIK, 5V)</strong>. Tüm bilgisayarlar, bu 0 ve 1'ler üzerinde "karar veren" temel <strong>Mantık Kapıları</strong> üzerine kuruludur.</p>
                
                <!-- İnteraktif Mantık Kapıları -->
                <div class="space-y-6">
                    <!-- AND Kapısı -->
                    <div class="bg-gray-100 p-6 rounded-lg border">
                        <h4 class="text-lg font-semibold">1. AND (VE) Kapısı</h4>
                        <p class="text-sm">Çıkış = 1 olması için A <strong>VE</strong> B = 1 olmalıdır.</p>
                        <div class="flex items-center gap-4 mt-4">
                            <div id="and-a" class="logic-switch off" data-gate="and" data-input="a">Giriş A (0)</div>
                            <span class="text-2xl font-bold">+</span>
                            <div id="and-b" class="logic-switch off" data-gate="and" data-input="b">Giriş B (0)</div>
                            <span class="text-2xl font-bold">=</span>
                            <div id="and-out" class="logic-light off"></div>
                        </div>
                    </div>
                    <!-- OR Kapısı -->
                    <div class="bg-gray-100 p-6 rounded-lg border">
                        <h4 class="text-lg font-semibold">2. OR (VEYA) Kapısı</h4>
                        <p class="text-sm">Çıkış = 1 olması için A <strong>VEYA</strong> B = 1 olması yeterlidir.</p>
                        <div class="flex items-center gap-4 mt-4">
                            <div id="or-a" class="logic-switch off" data-gate="or" data-input="a">Giriş A (0)</div>
                            <span class="text-2xl font-bold">+</span>
                            <div id="or-b" class="logic-switch off" data-gate="or" data-input="b">Giriş B (0)</div>
                            <span class="text-2xl font-bold">=</span>
                            <div id="or-out" class="logic-light off"></div>
                        </div>
                    </div>
                    <!-- NOT Kapısı -->
                    <div class="bg-gray-100 p-6 rounded-lg border">
                        <h4 class="text-lg font-semibold">3. NOT (DEĞİL) Kapısı (Tersleyici)</h4>
                        <p class="text-sm">Çıkış, girişin <strong>tersidir</strong>.</p>
                        <div class="flex items-center gap-4 mt-4">
                            <div id="not-a" class="logic-switch off" data-gate="not" data-input="a">Giriş A (0)</div>
                            <span class="text-2xl font-bold">=</span>
                            <div id="not-out" class="logic-light on"></div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Bölüm 9: Başarının Anahtarı -->
            <section id="bolum-9" class="bölüm-content hidden space-y-4">
                <h2 class="text-2xl font-bold text-blue-900 border-b-2 border-blue-200 pb-2">9. Özetle Başarının Anahtarı</h2>
                <p class="text-lg">Bu rehber, devre analizinin teorik temelini kapsamaktadır. Ancak gerçek ustalık, bu teoriyi pratikle birleştirmekten gelir. Başarı için yol haritanız:</p>
                
                <div class="space-y-4 mt-4">
                    <div class="bg-blue-50 p-6 rounded-lg border border-blue-200">
                        <h3 class="font-semibold text-xl text-blue-800">1. Matematik Yetkinliği</h3>
                        <p class="mt-2">Devre analizi, uygulamasının matematiğidir. Özellikle şu konularda akıcı olmalısınız:</p>
                        <ul class="list-disc list-inside ml-4 mt-2 space-y-1">
                            <li><strong>Doğrusal Denklem Sistemleri:</strong> Düğüm ve Göz analizlerini çözmek için matris kullanabilme.</li>
                            <li><strong>Karmaşık Sayılar:</strong> AC analizinin (Empedans, Fazör) temel dilidir. Dört işlem (toplama, çıkarma, çarpma, bölme) ve dönüşümler (kutupsal/kartezyen) konusunda çok yetkin olmalısınız.</li>
                        </ul>
                    </div>
                    
                    <div class="bg-green-50 p-6 rounded-lg border border-green-200">
                        <h3 class="font-semibold text-xl text-green-800">2. Sistematik Olma</h3>
                        <p class="mt-2">Her problem için doğru aracı seçebilmelisiniz. "Burada Thevenin mi kullanmalıyım, yoksa Düğüm Gerilimleri mi daha hızlı sonuç verir?" kararını verebilme becerisi, tecrübe ile kazanılır.</p>
                    </div>

                    <div class="bg-yellow-50 p-6 rounded-lg border border-yellow-200">
                        <h3 class="font-semibold text-xl text-yellow-800">3. Bol Pratik Yapma</h3>
                        <p class="mt-2">Devre analizi, bisiklete binmek gibidir; sadece okuyarak öğrenilmez. Çok sayıda ve farklı tipte problem çözmeniz, elinizi kirletmeniz gerekir.</p>
                    </div>

                    <div class="bg-purple-50 p-6 rounded-lg border border-purple-200">
                        <h3 class="font-semibold text-xl text-purple-800">4. Simülasyon Kullanma</h3>
                        <p class="mt-2">Teorik olarak bulduğunuz sonuçları doğrulamanın en iyi yolu simülasyondur. <strong>LTspice</strong> veya <strong>Multisim</strong> gibi bir simülasyon programı kullanarak çözümlerinizi doğrulayın. Bu, nerede hata yaptığınızı anlamanıza ve sezgi geliştirmenize yardımcı olur.</p>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {

            // --- SPA Navigasyon Mantığı ---
            const navLinks = document.querySelectorAll('.nav-link');
            const sections = document.querySelectorAll('.bölüm-content');
            
            function showSection(targetId) {
                sections.forEach(section => {
                    section.classList.add('hidden');
                });
                const targetSection = document.getElementById(targetId);
                if (targetSection) {
                    targetSection.classList.remove('hidden');
                }

                navLinks.forEach(link => {
                    link.classList.remove('active');
                    if (link.dataset.target === targetId) {
                        link.classList.add('active');
                    }
                });
            }

            navLinks.forEach(link => {
                link.addEventListener('click', (e) => {
                    e.preventDefault();
                    const targetId = e.target.closest('.nav-link').dataset.target;
                    showSection(targetId);
                });
            });

            // Başlangıçta Bölüm 0'ı göster
            showSection('bolum-0');

            // --- İnteraktif Widget Mantığı ---

            // Bölüm 0: Birim Dönüştürücü
            const unitValue = document.getElementById('unit-value');
            const unitFrom = document.getElementById('unit-from');
            const unitTo = document.getElementById('unit-to');
            const unitResult = document.getElementById('unit-result');

            function convertUnits() {
                const val = parseFloat(unitValue.value);
                const fromFactor = parseFloat(unitFrom.value);
                const toFactor = parseFloat(unitTo.value);
                
                if (isNaN(val) || isNaN(fromFactor) || isNaN(toFactor)) {
                    unitResult.textContent = 'Geçersiz değerler.';
                    return;
                }
                
                const result = (val * fromFactor) / toFactor;
                const fromText = unitFrom.options[unitFrom.selectedIndex].text;
                const toText = unitTo.options[unitTo.selectedIndex].text;
                
                unitResult.textContent = `${val} ${fromText} = ${result.toExponential(2)} ${toText}`;
            }
            [unitValue, unitFrom, unitTo].forEach(el => el.addEventListener('input', convertUnits));
            convertUnits(); // İlk yüklemede çalıştır

            // Bölüm 1: Ohm Kanunu Hesaplayıcı
            const ohmV = document.getElementById('ohm-v');
            const ohmI = document.getElementById('ohm-i');
            const ohmR = document.getElementById('ohm-r');
            const calcOhmBtn = document.getElementById('calc-ohm');
            const resetOhmBtn = document.getElementById('reset-ohm');
            const ohmResult = document.getElementById('ohm-result');

            calcOhmBtn.addEventListener('click', () => {
                const v = parseFloat(ohmV.value);
                const i = parseFloat(ohmI.value);
                const r = parseFloat(ohmR.value);

                if (isNaN(v) && !isNaN(i) && !isNaN(r)) {
                    const resultV = i * r;
                    ohmV.value = resultV.toFixed(2);
                    ohmResult.textContent = `Hesaplanan Gerilim: ${resultV.toFixed(2)} V`;
                } else if (!isNaN(v) && isNaN(i) && !isNaN(r)) {
                    if (r === 0) {
                        ohmResult.textContent = 'Hata: Direnç 0 olamaz (Kısa Devre)';
                        return;
                    }
                    const resultI = v / r;
                    ohmI.value = resultI.toFixed(2);
                    ohmResult.textContent = `Hesaplanan Akım: ${resultI.toFixed(2)} A`;
                } else if (!isNaN(v) && !isNaN(i) && isNaN(r)) {
                     if (i === 0) {
                        ohmResult.textContent = 'Hata: Akım 0 olamaz (Açık Devre)';
                        return;
                    }
                    const resultR = v / i;
                    ohmR.value = resultR.toFixed(2);
                    ohmResult.textContent = `Hesaplanan Direnç: ${resultR.toFixed(2)} Ω`;
                } else {
                    ohmResult.textContent = 'Lütfen hesaplanacak değeri boş bırakıp diğer iki değeri girin.';
                }
            });
            resetOhmBtn.addEventListener('click', () => {
                ohmV.value = ''; ohmI.value = ''; ohmR.value = ''; ohmResult.textContent = '';
            });

            // Bölüm 1: Gerilim Bölücü
            const vdVin = document.getElementById('vd-vin');
            const vdR1 = document.getElementById('vd-r1');
            const vdR2 = document.getElementById('vd-r2');
            const calcVdBtn = document.getElementById('calc-vd');
            const vdResult = document.getElementById('vd-result');

            function calculateVoltageDivider() {
                const vin = parseFloat(vdVin.value);
                const r1 = parseFloat(vdR1.value);
                const r2 = parseFloat(vdR2.value);
                if (isNaN(vin) || isNaN(r1) || isNaN(r2)) {
                    vdResult.textContent = 'Lütfen tüm değerleri girin.'; return;
                }
                if (r1 + r2 === 0) {
                    vdResult.textContent = 'Hata: Toplam direnç 0 olamaz.'; return;
                }
                const vout = vin * (r2 / (r1 + r2));
                vdResult.textContent = `Çıkış Gerilimi (V-out): ${vout.toFixed(2)} V`;
            }
            calcVdBtn.addEventListener('click', calculateVoltageDivider);
            [vdVin, vdR1, vdR2].forEach(input => input.addEventListener('input', calculateVoltageDivider));
            calculateVoltageDivider(); // İlk yüklemede çalıştır

            // Bölüm 5: Karmaşık Sayı Dönüştürücü
            const complexA = document.getElementById('complex-a');
            const complexB = document.getElementById('complex-b');
            const complexC_A = document.getElementById('complex-A');
            const complexPhi = document.getElementById('complex-phi');
            const toPolarBtn = document.getElementById('to-polar');
            const toCartesianBtn = document.getElementById('to-cartesian');
            const complexResult = document.getElementById('complex-result');

            toPolarBtn.addEventListener('click', () => {
                const a = parseFloat(complexA.value);
                const b = parseFloat(complexB.value);
                if (isNaN(a) || isNaN(b)) { complexResult.textContent = 'a ve b değerlerini girin.'; return; }
                const A = Math.sqrt(a*a + b*b);
                const phi = Math.atan2(b, a) * (180 / Math.PI);
                complexC_A.value = A.toFixed(3);
                complexPhi.value = phi.toFixed(3);
                complexResult.textContent = `Sonuç: ${A.toFixed(3)} ∠ ${phi.toFixed(3)}°`;
            });

            toCartesianBtn.addEventListener('click', () => {
                const A = parseFloat(complexC_A.value);
                const phi = parseFloat(complexPhi.value);
                if (isNaN(A) || isNaN(phi)) { complexResult.textContent = 'A ve θ değerlerini girin.'; return; }
                const phiRad = phi * (Math.PI / 180);
                const a = A * Math.cos(phiRad);
                const b = A * Math.sin(phiRad);
                complexA.value = a.toFixed(3);
                complexB.value = b.toFixed(3);
                complexResult.textContent = `Sonuç: ${a.toFixed(3)} + j(${b.toFixed(3)})`;
            });

            // Bölüm 6: Empedans Hesaplayıcı
            const impF = document.getElementById('imp-f');
            const impL = document.getElementById('imp-l');
            const impC = document.getElementById('imp-c');
            const calcImpBtn = document.getElementById('calc-imp');
            const impResult = document.getElementById('imp-result');
            
            calcImpBtn.addEventListener('click', () => {
                const f = parseFloat(impF.value);
                const l = parseFloat(impL.value) * 1e-3; // mH to H
                const c = parseFloat(impC.value) * 1e-6; // µF to F
                if (isNaN(f)) { impResult.textContent = 'Frekans girilmelidir.'; return; }
                const omega = 2 * Math.PI * f;
                let results = [];
                if (!isNaN(l) && l > 0) {
                    const ZL = omega * l;
                    results.push(`Z_L = j(${ZL.toFixed(2)} Ω)`);
                }
                if (!isNaN(c) && c > 0) {
                    const ZC = 1 / (omega * c);
                    results.push(`Z_C = -j(${ZC.toFixed(2)} Ω)`);
                }
                impResult.textContent = results.length > 0 ? results.join(' | ') : 'Hesaplamak için L veya C girin.';
            });

            // --- GRAFİKLER ---
            let charts = {}; // Grafikleri saklamak için
            
            function createRCChart() {
                if (charts.rc) charts.rc.destroy();
                const rcCtx = document.getElementById('rcChart').getContext('2d');
                const timePoints = Array.from({length: 51}, (_, i) => (i * 0.1).toFixed(1)); // 0 to 5 Tau
                const rcData = timePoints.map(t => Math.exp(-t)); // V(t) = e^(-t/tau) (Doğal tepki)
                charts.rc = new Chart(rcCtx, {
                    type: 'line', data: { labels: timePoints,
                        datasets: [{ label: 'Gerilim (V0 yüzdesi)', data: rcData, borderColor: '#2563eb', backgroundColor: 'rgba(59, 130, 246, 0.1)', fill: true, tension: 0.1, pointRadius: 0 }]
                    },
                    options: { responsive: true, maintainAspectRatio: false, scales: { x: { title: { display: true, text: 'Zaman (Zaman Sabiti τ cinsinden)' }}, y: { title: { display: true, text: 'Gerilim (Maksimum %)'}, min: 0, max: 1 }},
                        plugins: { tooltip: { callbacks: { label: (context) => `Gerilim: %${(context.raw * 100).toFixed(0)}` } } }
                    }
                });
            }

            function createACChart() {
                if (charts.ac) charts.ac.destroy();
                const acCtx = document.getElementById('acChart').getContext('2d');
                const acPoints = Array.from({length: 100}, (_, i) => i);
                const acData = acPoints.map(x => Math.sin(x * 2 * Math.PI / 50)); // 2 periyotluk sinüs dalgası
                charts.ac = new Chart(acCtx, {
                    type: 'line', data: { labels: acPoints,
                        datasets: [{ label: 'AC Gerilim', data: acData, borderColor: '#16a34a', fill: false, tension: 0.1, pointRadius: 0 }]
                    },
                    options: { responsive: true, maintainAspectRatio: false, scales: { x: { title: { display: true, text: 'Zaman' }, ticks: { display: false }}, y: { title: { display: true, text: 'Genlik' } } } }
                });
            }

            function createRLCChart() {
                if (charts.rlc) charts.rlc.destroy();
                const rlcCtx = document.getElementById('rlcChart').getContext('2d');
                const time = Array.from({length: 100}, (_, i) => i * 0.1);
                const overdamped = time.map(t => Math.exp(-t) - Math.exp(-2*t));
                const criticallyDamped = time.map(t => t * Math.exp(-t));
                const underdamped = time.map(t => Math.exp(-0.5 * t) * Math.sin(3 * t));
                
                charts.rlc = new Chart(rlcCtx, {
                    type: 'line', data: { labels: time,
                        datasets: [
                            { label: 'Eksik Sönümlü (Salınımlı)', data: underdamped, borderColor: '#db2777', fill: false, pointRadius: 0 },
                            { label: 'Aşırı Sönümlü (Yavaş)', data: overdamped, borderColor: '#2563eb', fill: false, pointRadius: 0 },
                            { label: 'Kritik Sönümlü (En Hızlı)', data: criticallyDamped, borderColor: '#16a34a', fill: false, pointRadius: 0 }
                        ]
                    },
                    options: { responsive: true, maintainAspectRatio: false, scales: { x: { title: { display: true, text: 'Zaman' }}, y: { title: { display: true, text: 'Tepki' } } },
                        plugins: { legend: { position: 'bottom' } }
                    }
                });
            }
            
            // Grafikleri sadece göründüklerinde oluştur
            const observer = new IntersectionObserver((entries) => {
                entries.forEach(entry => {
                    if (entry.isIntersecting) {
                        if (entry.target.id === 'rcChart' && !charts.rc) createRCChart();
                        if (entry.target.id === 'acChart' && !charts.ac) createACChart();
                        if (entry.target.id === 'rlcChart' && !charts.rlc) createRLCChart();
                    }
                });
            }, { threshold: 0.1 });

            document.getElementById('rcChart')._chartjs_initialized = false;
            document.getElementById('acChart')._chartjs_initialized = false;
            document.getElementById('rlcChart')._chartjs_initialized = false;
            
            observer.observe(document.getElementById('rcChart'));
            observer.observe(document.getElementById('acChart'));
            observer.observe(document.getElementById('rlcChart'));

            // Bonus: Mantık Kapıları
            const logicSwitches = document.querySelectorAll('.logic-switch');
            const logicState = {
                and: { a: 0, b: 0 },
                or: { a: 0, b: 0 },
                not: { a: 0 }
            };

            function updateLogicGates() {
                const andOut = document.getElementById('and-out');
                (logicState.and.a === 1 && logicState.and.b === 1) ? andOut.classList.add('on') : andOut.classList.remove('on');
                (logicState.and.a === 1 && logicState.and.b === 1) ? andOut.classList.remove('off') : andOut.classList.add('off');

                const orOut = document.getElementById('or-out');
                (logicState.or.a === 1 || logicState.or.b === 1) ? orOut.classList.add('on') : orOut.classList.remove('on');
                (logicState.or.a === 1 || logicState.or.b === 1) ? orOut.classList.remove('off') : orOut.classList.add('off');

                const notOut = document.getElementById('not-out');
                (logicState.not.a === 0) ? notOut.classList.add('on') : notOut.classList.remove('on');
                (logicState.not.a === 0) ? notOut.classList.remove('off') : notOut.classList.add('off');
            }

            logicSwitches.forEach(sw => {
                sw.addEventListener('click', () => {
                    const gate = sw.dataset.gate;
                    const input = sw.dataset.input;
                    const newState = logicState[gate][input] === 0 ? 1 : 0;
                    logicState[gate][input] = newState;
                    sw.classList.toggle('on', newState === 1);
                    sw.classList.toggle('off', newState === 0);
                    sw.textContent = `Giriş ${input.toUpperCase()} (${newState})`;
                    updateLogicGates();
                });
            });
            updateLogicGates(); // İlk durum

        });
    </script>
</body>
</html>
