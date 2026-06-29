#!/usr/bin/env python3
# =============================================================================
#  Sifre Guvenligi Analiz Araci
#  Hazirlayan : Nida Subasi
#  Aciklama   : Kisisel merak ve ogrenme amacli gelistirilmis bir sifre
#               analiz aracı. Tkinter, hashlib, secrets kutuphaneleri
#               kullanildi. Harici bagimlılık gerekmez; Python 3.6+ yeterli.
#  Surumu     : 2.0  |  2026
# =============================================================================

import tkinter as tk
from tkinter import ttk, messagebox
import re
import json
import math
import hashlib
import secrets
import string
import datetime
import os
import sys

# =============================================================================
#  RENK PALETİ  (GitHub Dark temasından esinlendim)
# =============================================================================
bg_main      = "#0d1117"
bg_sec       = "#161b22"
bg_third     = "#21262d"
border_color = "#30363d"
color_accent = "#58a6ff"
color_success = "#3fb950"
color_warn   = "#d29922"
color_danger = "#f85149"
text_main    = "#e6edf3"
text_muted   = "#8b949e"

font_title  = ("Consolas", 18, "bold")
font_normal = ("Consolas", 10)
font_small  = ("Consolas", 9)

# Gecmis kayitlari saklamak icin JSON dosyasi
DB_FILE = os.path.join(os.path.dirname(os.path.abspath(__file__)), "gecmis.json")

# Internette bulunan en yaygin sifreler — bunlar otomatik olarak zayif sayilir
YAYGIN_SIFRELER = [
    "123456", "password", "123456789", "12345678", "12345", "qwerty",
    "abc123", "111111", "123123", "admin", "letmein", "welcome",
    "monkey", "dragon", "master", "pass", "test", "sifre", "parola",
    "istanbul", "ankara", "turkiye", "sifre123", "1234", "iloveyou",
    "sunshine", "shadow", "football", "baseball", "login", "pass123",
]


# =============================================================================
#  VERİTABANI / GECMİS
# =============================================================================

def load_history():
    """Gecmis kayitlari JSON dosyasindan yukler."""
    if not os.path.exists(DB_FILE):
        return []
    try:
        with open(DB_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    except Exception:
        return []


def save_record(record):
    """Yeni bir analiz kaydini gecmise ekler."""
    data = load_history()
    data.append(record)
    try:
        with open(DB_FILE, "w", encoding="utf-8") as f:
            json.dump(data, f, ensure_ascii=False, indent=2)
    except Exception:
        pass  # Yazma hatasi olursa sessizce gec, analiz devam etsin


# =============================================================================
#  ANALIZ FONKSIYONLARI
# =============================================================================

def calculate_entropy(password_str):
    """Shannon entropisini hesaplar; daha yuksek = daha tahmin edilemez."""
    freq = {}
    for c in password_str:
        freq[c] = freq.get(c, 0) + 1
    n = len(password_str)
    ent = 0.0
    for count in freq.values():
        p = count / n
        ent -= p * math.log2(p)
    return round(ent, 2)


def check_password_strength(sifre):
    """
    Sifreyi uzunluk, karakter cesitliligi, tekrar ve yayginlik
    kriterlerine gore 0-100 arasi bir puana gore degerlendirir.
    """
    uzunluk = len(sifre)

    # Uzunluk puani
    if uzunluk >= 20:
        uz_puan = 30
    elif uzunluk >= 16:
        uz_puan = 25
    elif uzunluk >= 12:
        uz_puan = 20
    elif uzunluk >= 8:
        uz_puan = 12
    elif uzunluk >= 6:
        uz_puan = 6
    else:
        uz_puan = 2

    # Karakter tipi puanlari
    karakter_puani = 0
    kucuk  = bool(re.search(r'[a-z]', sifre))
    buyuk  = bool(re.search(r'[A-Z]', sifre))
    rakam  = bool(re.search(r'[0-9]', sifre))
    ozel   = bool(re.search(r'[!@#$%^&*()\-_=+\[\]{};:,.<>?]', sifre))
    tekrar = bool(re.search(r'(.)\1{2,}', sifre))
    yaygin = sifre.lower() in YAYGIN_SIFRELER

    if kucuk:  karakter_puani += 10
    if buyuk:  karakter_puani += 10
    if rakam:  karakter_puani += 10
    if ozel:   karakter_puani += 20
    if tekrar: karakter_puani -= 10  # ceza

    toplam = max(0, min(100, uz_puan + karakter_puani))

    # Yaygin sifrelere tavan koy
    if yaygin:
        toplam = min(toplam, 15)

    # Seviye belirleme
    if toplam >= 70:
        durum, renk = "COK GUCLU",  color_success
    elif toplam >= 55:
        durum, renk = "GUCLU",      color_success
    elif toplam >= 40:
        durum, renk = "ORTA",       color_warn
    elif toplam >= 25:
        durum, renk = "ZAYIF",      color_danger
    else:
        durum, renk = "COK ZAYIF",  color_danger

    # Brute-force suresi tahmini (yaklasik RTX 3090 hizinda)
    uzay = 0
    if kucuk: uzay += 26
    if buyuk: uzay += 26
    if rakam: uzay += 10
    if ozel:  uzay += 32
    if uzay == 0: uzay = 26

    kombinasyon = uzay ** uzunluk if uzunluk > 0 else 1
    saniye = (kombinasyon / 2) / 1_000_000_000

    if saniye < 1:
        bf_sure = "Aninda (<1 sn)"
    elif saniye < 60:
        bf_sure = f"{saniye:.1f} saniye"
    elif saniye < 3600:
        bf_sure = f"{saniye / 60:.1f} dakika"
    elif saniye < 86400:
        bf_sure = f"{saniye / 3600:.1f} saat"
    elif saniye < 31_536_000:
        bf_sure = f"{saniye / 86400:.1f} gun"
    elif saniye < 31_536_000 * 1000:
        bf_sure = f"{saniye / 31_536_000:.1f} yil"
    else:
        bf_sure = f"{saniye / 31_536_000:.2e} yil"

    return {
        "puan": toplam, "seviye": durum, "renk": renk,
        "uzunluk": uzunluk, "entropi": calculate_entropy(sifre),
        "has_lower": kucuk, "has_upper": buyuk,
        "has_digit": rakam, "has_spec": ozel,
        "has_rep": tekrar, "is_common": yaygin,
        "brute_force": bf_sure,
        "uzunluk_puan": uz_puan, "karakter_puan": karakter_puani,
    }


def generate_hashes(sifre):
    """Verilen sifrenin MD5, SHA1, SHA256 ve SHA512 hashlerini hesaplar."""
    sonuclar = {}
    for algo in ["md5", "sha1", "sha256", "sha512"]:
        sonuclar[algo] = hashlib.new(algo, sifre.encode()).hexdigest()
    return sonuclar


def create_random_password(length=16, use_special=True):
    """Kriptografik olarak guvenli rastgele sifre uretir."""
    chars = string.ascii_letters + string.digits
    if use_special:
        chars += "!@#$%^&*()-_=+"
    return ''.join(secrets.choice(chars) for _ in range(length))


# =============================================================================
#  WIDGET YARDIMCILARI
# =============================================================================

class CustomCard(tk.Frame):
    """Cerceveli, koyu temali kart bileseni."""
    def __init__(self, parent, **kwargs):
        super().__init__(
            parent,
            bg=bg_sec,
            highlightbackground=border_color,
            highlightthickness=1,
            **kwargs
        )


class PasswordInputBox(tk.Frame):
    """Goster/gizle butonu olan sifre giris alani."""

    def __init__(self, parent, **kwargs):
        super().__init__(parent, bg=bg_sec)
        self.is_hidden = True

        self.entry = tk.Entry(
            self, show="●",
            font=("Courier New", 10),
            bg=bg_third, fg=text_main,
            insertbackground=color_accent,
            relief="flat", bd=8,
            **kwargs
        )
        self.entry.pack(side="left", fill="x", expand=True)

        self.toggle_btn = tk.Button(
            self, text="👁",
            font=("Segoe UI Emoji" if sys.platform == "win32" else "TkDefaultFont", 10),
            bg=bg_third, fg=text_muted,
            relief="flat", cursor="hand2",
            command=self.toggle_visibility, bd=0
        )
        self.toggle_btn.pack(side="right", padx=4)

    def toggle_visibility(self):
        self.is_hidden = not self.is_hidden
        if self.is_hidden:
            self.entry.config(show="●")
            self.toggle_btn.config(text="👁")
        else:
            self.entry.config(show="")
            self.toggle_btn.config(text="🔒")

    def get_text(self):
        return self.entry.get()


# =============================================================================
#  ANA UYGULAMA
# =============================================================================

class MainApp(tk.Tk):

    def __init__(self):
        super().__init__()
        self.title("Sifre Guvenligi Analiz Araci  |  Nida Subasi  |  2026")
        self.geometry("900x680")
        self.minsize(820, 620)
        self.configure(bg=bg_main)

        # macOS'ta pencereyi ortala
        self.update_idletasks()
        w = self.winfo_width()
        h = self.winfo_height()
        x = (self.winfo_screenwidth()  - w) // 2
        y = (self.winfo_screenheight() - h) // 2
        self.geometry(f"+{x}+{y}")

        self.setup_header()
        self.setup_navbar()
        self.setup_pages()
        self.setup_statusbar()
        self.switch_tab("analiz")

    # ---- BASLIK ----

    def setup_header(self):
        top_bar = tk.Frame(
            self, bg=bg_sec, height=56,
            highlightbackground=border_color, highlightthickness=1
        )
        top_bar.pack(fill="x")
        top_bar.pack_propagate(False)

        tk.Label(
            top_bar, text="🔐",
            font=("Segoe UI Emoji" if sys.platform == "win32" else "TkDefaultFont", 20),
            bg=bg_sec, fg=color_accent
        ).pack(side="left", padx=(18, 6), pady=8)

        tk.Label(
            top_bar, text="Sifre Guvenligi Analiz Araci",
            font=font_title, bg=bg_sec, fg=text_main
        ).pack(side="left")

        tk.Label(
            top_bar, text="Nida Subasi  |  2026",
            font=font_small, bg=bg_sec, fg=color_accent
        ).pack(side="right", padx=(0, 14))

        tk.Label(
            top_bar, text="Bilisim Guvenligi  |",
            font=font_small, bg=bg_sec, fg=text_muted
        ).pack(side="right", padx=(18, 0))

    # ---- NAVIGASYON ----

    def setup_navbar(self):
        self.nav_frame = tk.Frame(
            self, bg=bg_third, height=42,
            highlightbackground=border_color, highlightthickness=1
        )
        self.nav_frame.pack(fill="x")
        self.nav_frame.pack_propagate(False)

        self.buttons = {}
        tabs = [
            ("analiz",  "🔍 Analiz"),
            ("uretici", "⚙ Uretici"),
            ("hash",    "# Hash"),
            ("gecmis",  "📋 Gecmis"),
        ]
        for key, label in tabs:
            btn = tk.Button(
                self.nav_frame, text=label,
                font=font_small, bg=bg_third, fg=text_muted,
                relief="flat", cursor="hand2",
                padx=18, pady=10, bd=0,
                command=lambda k=key: self.switch_tab(k)
            )
            btn.pack(side="left")
            self.buttons[key] = btn

    # ---- SAYFALAR ----

    def setup_pages(self):
        self.content_area = tk.Frame(self, bg=bg_main)
        self.content_area.pack(fill="both", expand=True)
        self.pages = {}
        for func, name in [
            (self.render_analiz,  "analiz"),
            (self.render_uretici, "uretici"),
            (self.render_hash,    "hash"),
            (self.render_gecmis,  "gecmis"),
        ]:
            frame = tk.Frame(self.content_area, bg=bg_main)
            func(frame)
            self.pages[name] = frame

    # ---- DURUM CUBUGU ----

    def setup_statusbar(self):
        self.status_msg = tk.StringVar(value="Sistem Hazir")
        bar = tk.Frame(
            self, bg=bg_sec, height=26,
            highlightbackground=border_color, highlightthickness=1
        )
        bar.pack(fill="x", side="bottom")
        bar.pack_propagate(False)

        tk.Label(
            bar, textvariable=self.status_msg,
            font=font_small, bg=bg_sec, fg=text_muted, anchor="w"
        ).pack(side="left", padx=12)

        # Kalici imza — sag alt kose
        tk.Label(
            bar, text="Nida Subasi  |  2026",
            font=font_small, bg=bg_sec, fg=border_color, anchor="e"
        ).pack(side="right", padx=12)

    def update_status(self, msg):
        self.status_msg.set(msg)

    def switch_tab(self, tab_name):
        for k, f in self.pages.items():
            f.pack_forget()
        self.pages[tab_name].pack(fill="both", expand=True)
        for k, btn in self.buttons.items():
            if k == tab_name:
                btn.config(
                    bg=bg_main, fg=color_accent,
                    highlightbackground=color_accent, highlightthickness=1
                )
            else:
                btn.config(
                    bg=bg_third, fg=text_muted,
                    highlightbackground=bg_third, highlightthickness=0
                )
        self.update_status(f"Sekme: {tab_name.capitalize()}")

    # ---- SEKME 1: ANALİZ ----

    def render_analiz(self, parent_frame):
        input_card = CustomCard(parent_frame, pady=16, padx=20)
        input_card.pack(fill="x", padx=20, pady=(20, 10))

        tk.Label(
            input_card, text="Analiz Edilecek Sifre",
            font=("Consolas", 12, "bold"), bg=bg_sec, fg=color_accent
        ).pack(anchor="w", pady=(0, 8))

        self.pass_entry = PasswordInputBox(input_card)
        self.pass_entry.pack(fill="x", pady=(0, 4))
        self.pass_entry.entry.bind("<KeyRelease>", self.do_live_analysis)

        tk.Label(
            input_card,
            text="Yazinca otomatik analiz yapar. Sifreler disari aktarilmaz.",
            font=font_small, bg=bg_sec, fg=text_muted
        ).pack(anchor="w")

        result_area = tk.Frame(parent_frame, bg=bg_main)
        result_area.pack(fill="both", expand=True, padx=20, pady=(0, 16))
        result_area.columnconfigure(0, weight=1)
        result_area.columnconfigure(1, weight=1)

        # Sol panel
        left_panel = CustomCard(result_area, pady=14, padx=16)
        left_panel.grid(row=0, column=0, sticky="nsew", padx=(0, 8))

        tk.Label(
            left_panel, text="Degerlendirme",
            font=("Consolas", 12, "bold"), bg=bg_sec, fg="#c9d1d9"
        ).pack(anchor="w", pady=(0, 10))

        self.score_lbl = tk.Label(
            left_panel, text="—",
            font=("Consolas", 32, "bold"), bg=bg_sec, fg=text_muted
        )
        self.score_lbl.pack()

        self.level_lbl = tk.Label(
            left_panel, text="Bekleniyor...",
            font=("Consolas", 12, "bold"), bg=bg_sec, fg=text_muted
        )
        self.level_lbl.pack(pady=(2, 10))

        self.canvas_bar = tk.Canvas(
            left_panel, height=18,
            bg=bg_third, highlightthickness=0, bd=0
        )
        self.canvas_bar.pack(fill="x", pady=(0, 14))
        self.bar_fill = self.canvas_bar.create_rectangle(
            0, 0, 0, 18, fill=color_success, outline=""
        )

        tk.Frame(left_panel, bg=border_color, height=1).pack(fill="x", pady=8)
        tk.Label(
            left_panel, text="Karakter Detaylari",
            font=("Consolas", 12, "bold"), bg=bg_sec, fg="#c9d1d9"
        ).pack(anchor="w", pady=(0, 6))

        self.char_frame = tk.Frame(left_panel, bg=bg_sec)
        self.char_frame.pack(fill="x")
        self.char_labels = {}
        char_types = [
            ("lower", "Kucuk Harf (a-z)"),
            ("upper", "Buyuk Harf (A-Z)"),
            ("digit", "Rakam (0-9)"),
            ("spec",  "Ozel Karakter"),
            ("rep",   "Tekrar Eden Harf"),
            ("comm",  "Yaygin Sifre mi?"),
        ]
        for key, label in char_types:
            row = tk.Frame(self.char_frame, bg=bg_sec)
            row.pack(fill="x", pady=1)
            tk.Label(
                row, text=label, font=font_small, bg=bg_sec,
                fg=text_muted, width=22, anchor="w"
            ).pack(side="left")
            lbl = tk.Label(row, text="—", font=font_small, bg=bg_sec, fg=text_muted)
            lbl.pack(side="left")
            self.char_labels[key] = lbl

        # Sag panel
        right_panel = CustomCard(result_area, pady=14, padx=16)
        right_panel.grid(row=0, column=1, sticky="nsew", padx=(8, 0))

        tk.Label(
            right_panel, text="Detayli Rapor",
            font=("Consolas", 12, "bold"), bg=bg_sec, fg="#c9d1d9"
        ).pack(anchor="w", pady=(0, 8))

        self.detail_frame = tk.Frame(right_panel, bg=bg_sec)
        self.detail_frame.pack(fill="x")
        self.detail_labels = {}
        details = [
            ("len", "Uzunluk"),
            ("ent", "Entropi (Shannon)"),
            ("lp",  "Uzunluk Puani"),
            ("cp",  "Karakter Puani"),
            ("bf",  "GPU Brute-Force"),
        ]
        for key, label in details:
            row = tk.Frame(self.detail_frame, bg=bg_sec)
            row.pack(fill="x", pady=1)
            tk.Label(
                row, text=label + ":", font=font_small,
                bg=bg_sec, fg=text_muted, width=22, anchor="w"
            ).pack(side="left")
            lbl = tk.Label(row, text="—", font=font_small, bg=bg_sec, fg="#c9d1d9")
            lbl.pack(side="left")
            self.detail_labels[key] = lbl

        tk.Frame(right_panel, bg=border_color, height=1).pack(fill="x", pady=10)
        tk.Label(
            right_panel, text="Ne Yapilmali?",
            font=("Consolas", 12, "bold"), bg=bg_sec, fg="#c9d1d9"
        ).pack(anchor="w", pady=(0, 6))

        self.tips_box = tk.Text(
            right_panel, font=font_small,
            bg=bg_third, fg=color_warn,
            relief="flat", bd=6, height=6,
            wrap="word", state="disabled"
        )
        self.tips_box.pack(fill="both", expand=True)

    def do_live_analysis(self, event=None):
        """Kullanici yazarken gercek zamanli analiz yapar."""
        password = self.pass_entry.get_text()

        if not password:
            self.score_lbl.config(text="—", fg=text_muted)
            self.level_lbl.config(text="Sifre giriniz", fg=text_muted)
            self.canvas_bar.coords(self.bar_fill, 0, 0, 0, 18)
            for lbl in self.char_labels.values():
                lbl.config(text="—", fg=text_muted)
            for lbl in self.detail_labels.values():
                lbl.config(text="—", fg="#c9d1d9")
            self.write_tips("")
            return

        res  = check_password_strength(password)
        puan = res["puan"]

        self.score_lbl.config(text=f"{puan}/100", fg=res["renk"])
        self.level_lbl.config(text=res["seviye"],  fg=res["renk"])

        self.canvas_bar.update_idletasks()
        w       = self.canvas_bar.winfo_width()
        doluluk = int((puan / 100) * w)
        self.canvas_bar.coords(self.bar_fill, 0, 0, doluluk, 18)
        self.canvas_bar.itemconfig(self.bar_fill, fill=res["renk"])

        def chk(cond, reverse=False):
            """Kosula gore renk ve etiket doner."""
            good = not reverse
            if cond:
                return ("✓ Var",  color_success if good  else color_danger)
            else:
                return ("✗ Yok",  color_danger  if good  else color_success)

        self.char_labels["lower"].config(*chk(res["has_lower"]))
        self.char_labels["upper"].config(*chk(res["has_upper"]))
        self.char_labels["digit"].config(*chk(res["has_digit"]))
        self.char_labels["spec"].config(*chk(res["has_spec"]))
        self.char_labels["rep"].config(*chk(res["has_rep"], reverse=True))

        yaygin_text = "⚠ Evet" if res["is_common"] else "✓ Hayir"
        yaygin_renk = color_danger if res["is_common"] else color_success
        self.char_labels["comm"].config(text=yaygin_text, fg=yaygin_renk)

        self.detail_labels["len"].config(text=f"{res['uzunluk']} char")
        self.detail_labels["ent"].config(text=f"{res['entropi']} bit")
        self.detail_labels["lp"].config(text=f"+{res['uzunluk_puan']}")
        self.detail_labels["cp"].config(text=f"{res['karakter_puan']:+d}")
        self.detail_labels["bf"].config(text=res["brute_force"])

        # Tavsiyeler
        tavsiyeler = []
        if res["uzunluk"] < 12:
            tavsiyeler.append("- Sifre cok kisa, en az 12 karakter olsun.")
        if not res["has_upper"]:
            tavsiyeler.append("- Buyuk harf ekle (A-Z).")
        if not res["has_digit"]:
            tavsiyeler.append("- En az bir rakam ekle.")
        if not res["has_spec"]:
            tavsiyeler.append("- Ozel karakter kullan (!?# gibi).")
        if res["has_rep"]:
            tavsiyeler.append("- Ayni harfi cok tekrar etmissin.")
        if res["is_common"]:
            tavsiyeler.append("- BU EN YAYGIN SIFRELERDEN BIRI! HEMEN DEGISTIR!")
        if res["entropi"] < 2.5:
            tavsiyeler.append("- Entropi cok dusuk; daha cesitli karakterler kullan.")
        if not tavsiyeler:
            tavsiyeler.append("✓ Guzel gorunuyor, ekleyecek bir sey yok.")

        self.write_tips("\n".join(tavsiyeler))

        save_record({
            "tarih":   datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            "uzunluk": res["uzunluk"],
            "puan":    puan,
            "seviye":  res["seviye"],
            "entropi": res["entropi"],
        })
        self.update_status(f"Analiz tamam -> Puan: {puan}/100")

    def write_tips(self, metin):
        self.tips_box.config(state="normal")
        self.tips_box.delete("1.0", "end")
        self.tips_box.insert("1.0", metin)
        self.tips_box.config(state="disabled")

    # ---- SEKME 2: URETICI ----

    def render_uretici(self, parent):
        card = CustomCard(parent, pady=20, padx=22)
        card.pack(fill="both", expand=True, padx=20, pady=20)

        tk.Label(
            card, text="⚙ Guvenli Sifre Uret",
            font=("Consolas", 12, "bold"), bg=bg_sec, fg=color_accent
        ).pack(anchor="w", pady=(0, 16))

        ayarlar = tk.Frame(card, bg=bg_sec)
        ayarlar.pack(fill="x", pady=(0, 14))

        tk.Label(ayarlar, text="Uzunluk:", font=font_small,
                 bg=bg_sec, fg=text_muted).pack(side="left")
        self.uzunluk_val = tk.IntVar(value=16)
        tk.Spinbox(
            ayarlar, from_=8, to=64, textvariable=self.uzunluk_val,
            font=font_normal, bg=bg_third, fg=text_main,
            width=5, relief="flat", buttonbackground=bg_third
        ).pack(side="left", padx=(6, 20))

        tk.Label(ayarlar, text="Kac Tane:", font=font_small,
                 bg=bg_sec, fg=text_muted).pack(side="left")
        self.count_val = tk.IntVar(value=5)
        tk.Spinbox(
            ayarlar, from_=1, to=20, textvariable=self.count_val,
            font=font_normal, bg=bg_third, fg=text_main,
            width=4, relief="flat", buttonbackground=bg_third
        ).pack(side="left", padx=(6, 20))

        self.use_specials = tk.BooleanVar(value=True)
        tk.Checkbutton(
            ayarlar, text="Ozel Karakter Olsun",
            variable=self.use_specials, font=font_small,
            bg=bg_sec, fg=text_muted, selectcolor=bg_third,
            activebackground=bg_sec, activeforeground=text_main
        ).pack(side="left")

        tk.Button(
            card, text="  Uret  ",
            font=("Consolas", 12, "bold"),
            bg=color_accent, fg="#0d1117",
            relief="flat", cursor="hand2",
            pady=8, command=self.generate_passwords_ui
        ).pack(anchor="w", pady=(0, 14))

        tk.Label(card, text="Sonuclar:", font=font_small,
                 bg=bg_sec, fg=text_muted).pack(anchor="w", pady=(0, 4))

        list_frame = tk.Frame(
            card, bg=bg_third,
            highlightbackground=border_color, highlightthickness=1
        )
        list_frame.pack(fill="both", expand=True)

        scroll = tk.Scrollbar(list_frame)
        scroll.pack(side="right", fill="y")

        self.pass_listbox = tk.Listbox(
            list_frame, font=("Courier New", 10),
            bg=bg_third, fg=text_main,
            selectbackground=color_accent, selectforeground="#0d1117",
            relief="flat", bd=8,
            yscrollcommand=scroll.set, activestyle="none"
        )
        self.pass_listbox.pack(fill="both", expand=True)
        scroll.config(command=self.pass_listbox.yview)

        tk.Label(
            card, text="Kopyalamak icin tikla, Ctrl+C yap.",
            font=font_small, bg=bg_sec, fg=text_muted
        ).pack(anchor="w", pady=(6, 0))

    def generate_passwords_ui(self):
        self.pass_listbox.delete(0, "end")
        length   = self.uzunluk_val.get()
        amount   = self.count_val.get()
        specials = self.use_specials.get()
        for i in range(amount):
            pw     = create_random_password(length, specials)
            analiz = check_password_strength(pw)
            self.pass_listbox.insert(
                "end",
                f" [{i + 1:>2}]  {pw}   ->  Puan: {analiz['puan']} ({analiz['seviye']})"
            )
        self.update_status(f"{amount} adet sifre olusturuldu.")

    # ---- SEKME 3: HASH ----

    def render_hash(self, parent):
        card = CustomCard(parent, pady=20, padx=22)
        card.pack(fill="both", expand=True, padx=20, pady=20)

        tk.Label(
            card, text="# Hash Simulasyonu",
            font=("Consolas", 12, "bold"), bg=bg_sec, fg=color_accent
        ).pack(anchor="w", pady=(0, 6))
        tk.Label(
            card,
            text="Gercek sistemlerde sifre duz metin olarak saklanmaz; hash'lenir.",
            font=font_small, bg=bg_sec, fg=text_muted
        ).pack(anchor="w", pady=(0, 14))

        g_frame = tk.Frame(card, bg=bg_sec)
        g_frame.pack(fill="x", pady=(0, 14))

        tk.Label(g_frame, text="Sifre:", font=font_small,
                 bg=bg_sec, fg=text_muted).pack(side="left")
        self.hash_input = PasswordInputBox(g_frame)
        self.hash_input.pack(side="left", fill="x", expand=True, padx=(8, 12))

        tk.Button(
            g_frame, text="  Hash Cikar  ",
            font=font_small, bg=color_accent, fg="#0d1117",
            relief="flat", cursor="hand2", command=self.do_hash_calc
        ).pack(side="left")

        tk.Frame(card, bg=border_color, height=1).pack(fill="x", pady=(0, 12))

        self.hash_results = {}
        algo_data = {
            "md5":    (color_danger,  "⚠ Guvensiz (sadece dosya dogrulama icin)"),
            "sha1":   (color_warn,    "⚠ Zayifladi (collision riski var)"),
            "sha256": (color_success, "✓ Modern ve Guvenli"),
            "sha512": (color_accent,  "✓ Cok Guclu"),
        }
        for alg, (renk, not_txt) in algo_data.items():
            box = tk.Frame(
                card, bg=bg_third,
                highlightbackground=renk, highlightthickness=1
            )
            box.pack(fill="x", pady=4)

            top_part = tk.Frame(box, bg=bg_third)
            top_part.pack(fill="x", padx=10, pady=(6, 2))

            tk.Label(
                top_part, text=alg.upper(),
                font=("Consolas", 12, "bold"), bg=bg_third, fg=renk
            ).pack(side="left")
            tk.Label(
                top_part, text=not_txt,
                font=font_small, bg=bg_third, fg=text_muted
            ).pack(side="right")

            lbl = tk.Label(
                box, text="...",
                font=("Courier New", 10),
                bg=bg_third, fg="#c9d1d9",
                wraplength=760, anchor="w"
            )
            lbl.pack(fill="x", padx=10, pady=(0, 6))
            self.hash_results[alg] = lbl

    def do_hash_calc(self):
        txt = self.hash_input.get_text()
        if not txt:
            messagebox.showwarning(
                "Uyari", "Bos sifrenin hash'ini hesaplayamam.", parent=self
            )
            return
        sonuclar = generate_hashes(txt)
        for alg, h in sonuclar.items():
            self.hash_results[alg].config(text=h)
        self.update_status("Hash hesaplamasi tamamlandi.")

    # ---- SEKME 4: GECMİS ----

    def render_gecmis(self, parent):
        card = CustomCard(parent, pady=20, padx=22)
        card.pack(fill="both", expand=True, padx=20, pady=20)

        top_bar = tk.Frame(card, bg=bg_sec)
        top_bar.pack(fill="x", pady=(0, 14))

        tk.Label(
            top_bar, text="📋 Son Analiz Kayitlari",
            font=("Consolas", 12, "bold"), bg=bg_sec, fg=color_accent
        ).pack(side="left")
        tk.Button(
            top_bar, text="  Listeyi Guncelle  ",
            font=font_small, bg=bg_third, fg=text_muted,
            relief="flat", cursor="hand2",
            command=self.load_history_ui
        ).pack(side="right")

        cols = ("Sira", "Tarih", "Uzunluk", "Puan", "Durum", "Entropi")
        s = ttk.Style()
        s.theme_use("default")
        s.configure(
            "Tablo.Treeview",
            background=bg_third, foreground="#c9d1d9",
            fieldbackground=bg_third, rowheight=24,
            font=font_small, borderwidth=0
        )
        s.configure(
            "Tablo.Treeview.Heading",
            background=bg_sec, foreground=color_accent,
            font=font_small, relief="flat"
        )
        s.map(
            "Tablo.Treeview",
            background=[("selected", color_accent)],
            foreground=[("selected", "#0d1117")]
        )

        t_frame = tk.Frame(
            card, bg=bg_third,
            highlightbackground=border_color, highlightthickness=1
        )
        t_frame.pack(fill="both", expand=True)

        sc = tk.Scrollbar(t_frame)
        sc.pack(side="right", fill="y")

        self.table = ttk.Treeview(
            t_frame, columns=cols, show="headings",
            style="Tablo.Treeview", yscrollcommand=sc.set
        )
        widths = [40, 160, 80, 70, 100, 80]
        for c, w in zip(cols, widths):
            self.table.heading(c, text=c)
            self.table.column(c, width=w, anchor="center")
        self.table.pack(fill="both", expand=True)
        sc.config(command=self.table.yview)

        self.stat_label = tk.Label(
            card, text="", font=font_small,
            bg=bg_sec, fg=text_muted, anchor="w"
        )
        self.stat_label.pack(fill="x", pady=(10, 0))

        self.load_history_ui()

    def load_history_ui(self):
        """Gecmis tablosunu JSON dosyasindan gunceller."""
        for item in self.table.get_children():
            self.table.delete(item)
        kayitlar = load_history()
        if not kayitlar:
            self.stat_label.config(text="Henuz analiz kaydi yok.")
            return
        for idx, k in enumerate(reversed(kayitlar[-50:]), 1):
            self.table.insert("", "end", values=(
                idx,
                k.get("tarih",   "-"),
                k.get("uzunluk", "-"),
                k.get("puan",    "-"),
                k.get("seviye",  "-"),
                k.get("entropi", "-"),
            ))
        puanlar = [x.get("puan", 0) for x in kayitlar]
        ort = sum(puanlar) / len(puanlar)
        self.stat_label.config(
            text=(
                f"Toplam: {len(kayitlar)} kayit  |  "
                f"Ortalama: {ort:.1f}  |  "
                f"Max: {max(puanlar)}  |  "
                f"Min: {min(puanlar)}"
            )
        )
        self.update_status("Gecmis verileri yuklendi.")


# =============================================================================
#  GİRİS NOKTASI
# =============================================================================

if __name__ == "__main__":
    uygulama = MainApp()
    uygulama.mainloop()
