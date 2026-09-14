# AHA 1.3 — Automatic Head Anonymization

> Ένα βίντεο μπαίνει, ένα δεύτερο βγαίνει με τα κεφάλια των ανθρώπων κρυμμένα.
> Το πρωτότυπο δεν αγγίζεται ποτέ. Τρέχει 100% τοπικά.
>
> *A video goes in, a second one comes out with people's heads hidden.
> The original is never touched. Runs 100% locally.*

**Δημιουργός / Author:** Πέτρος Ανδριανός · ™ 2026 · © Copyright by Petros Andrianos
**Python + OpenCV · Windows**

🇬🇷 **[Ελληνικά](#ελληνικά)** · 🇬🇧 **[English](#english)**

---

# Ελληνικά

## Περιεχόμενα

1. [Τι κάνει το πρόγραμμα](#1-τι-κάνει-το-πρόγραμμα)
2. [Τι χρειάζεται για να τρέξει](#2-τι-χρειάζεται-για-να-τρέξει)
3. [Η διαδικασία, βήμα βήμα](#3-η-διαδικασία-βήμα-βήμα)
4. [Τα πέντε εφέ](#4-τα-πέντε-εφέ)
5. [Κάθε χειριστήριο](#5-κάθε-χειριστήριο)
6. [Όταν κάτι πάει στραβά](#6-όταν-κάτι-πάει-στραβά)
7. [Το όριο που πρέπει να ξέρεις](#7-το-όριο-που-πρέπει-να-ξέρεις)
8. [ΓΚΠΔ](#8-γκπδ)

---

## 1. Τι κάνει το πρόγραμμα

Το AHA είναι εργαλείο γραφείου, όχι υπηρεσία. Τρέχει εξ ολοκλήρου στον υπολογιστή σου:
κανένα καρέ δεν φεύγει από τη μηχανή, δεν υπάρχει τηλεμετρία και δεν κρατιέται κανένα
αρχείο καταγραφής με προσωπικά δεδομένα. Η μόνη φορά που αγγίζει το δίκτυο είναι την
πρώτη εκτέλεση, αν λείπει το αρχείο του μοντέλου ανίχνευσης — το κατεβάζει από το
GitHub και δεν στέλνει τίποτα.

Το πρωτότυπο μένει ανέπαφο: γράφεται πάντα **νέο** αρχείο, με το όνομα του πρωτοτύπου
και το εφέ στο τέλος (`TestFile_BlackBars.mp4`).

Κάνει **ανίχνευση** προσώπου, όχι **αναγνώριση**. Δεν φτιάχνεται και δεν αποθηκεύεται
κανένα βιομετρικό αποτύπωμα· βρίσκει πού υπάρχει κεφάλι και το σβήνει.

### Δύο διαφορετικά προβλήματα, δύο λειτουργίες

| | **Πρόσωπα (κάθε βίντεο)** | **CCTV (σταθερή κάμερα)** |
|---|---|---|
| Πώς δουλεύει | Ανιχνευτής προσώπου σε κάθε καρέ ξεχωριστά | Μαθαίνει πρώτα τον **άδειο** χώρο· ό,τι διαφέρει είναι άνθρωπος |
| Υλικό | Οποιοδήποτε — κάμερα στο χέρι, κινητό, drone | Απαιτεί **ακίνητη** κάμερα |
| Άνθρωπος από πίσω | ❌ δεν πιάνεται | ✅ πιάνεται |
| Χρειάζεται ορατό πρόσωπο | ναι | όχι |

> **Για τα Duty Free:** χρησιμοποίησε τη λειτουργία **CCTV**. Οι κάμερες είναι σταθερές
> και ψηλά, οι άνθρωποι περιφέρονται ανάμεσα σε ράφια που κρύβουν το σώμα τους, και πολύ
> συχνά δεν κοιτάζουν ποτέ την κάμερα — ακριβώς η περίπτωση όπου ένας ανιχνευτής
> προσώπου δεν αρκεί.

---

## 2. Τι χρειάζεται για να τρέξει

| Τι | Λεπτομέρεια | Αν λείπει |
|---|---|---|
| Python | `3.9+` | Το πρόγραμμα δεν ανοίγει. |
| OpenCV | `pip install opencv-python` | Δοκιμασμένο σε 5.0.0. |
| NumPy | `pip install numpy` | Έρχεται μαζί με το OpenCV. |
| Μοντέλο | `face_detection_yunet_2023mar.onnx` | Κατεβαίνει μόνο του δίπλα στο script. |
| ffmpeg | στο `PATH` | Προαιρετικό. Χωρίς αυτό δεν κρατιέται ήχος και δεν αφαιρούνται μεταδεδομένα. |

Οι ρυθμίσεις γράφονται στο `aha_settings.json`, δίπλα στο `AHA_1.3.py`. Σβήσε το αρχείο
για να ξαναρχίσεις από τις προεπιλογές.

```
python AHA_1.3.py
```

Το παράθυρο ανοίγει μεγιστοποιημένο.

---

## 3. Η διαδικασία, βήμα βήμα

Η σειρά έχει σημασία: **η αναφορά πρέπει να χτιστεί πριν δεις σωστή προεπισκόπηση.**

**1. Διάλεξε το βίντεο.** `Άνοιγμα...` στην **Είσοδο**. Το πεδίο **Έξοδος**
συμπληρώνεται μόνο του: *όνομα εισόδου + το εφέ*.

```
Είσοδος:  F:/Videos/TestFile.mp4
Έξοδος:   F:/Videos/TestFile_BlackBars.mp4
```

Αλλάζεις εφέ, αλλάζει και το όνομα — έτσι δύο εξαγωγές του ίδιου κλιπ δεν πατάει η μία
την άλλη. Αν γράψεις ή διαλέξεις δικό σου όνομα, το πρόγραμμα δεν το ξαναπειράζει.

**2. Διάλεξε λειτουργία.** Στην κάρτα **ΑΝΙΧΝΕΥΣΗ**: *Πρόσωπα (κάθε βίντεο)* ή
*CCTV (σταθερή κάμερα)*.

**3. Μόνο για CCTV: χτίσε την αναφορά.** Το `Χτίσιμο αναφοράς` δειγματοληπτεί 60 καρέ
από όλο το βίντεο και κρατά τη διάμεση τιμή κάθε εικονοστοιχείου· οι άνθρωποι, που είναι
παροδικοί, εξαφανίζονται και μένει το άδειο κατάστημα. Παίρνει λίγα δευτερόλεπτα.
**Χωρίς αυτό, η λειτουργία CCTV δεν ανιχνεύει τίποτα.**

**4. Έλεγξε στην προεπισκόπηση.** Σύρε τη μπάρα **Καρέ** σε διάφορα σημεία. Τσέκαρε τα
**Πλαίσια ανίχνευσης** για να δεις πού ακριβώς πέφτουν τα σημάδια. *Αυτό είναι το βήμα
που κρίνει το αποτέλεσμα — μη το προσπερνάς.*

**5. Ρύθμισε ό,τι χρειάζεται.** Χάνονται άνθρωποι; Κατέβασε το **ΚΑΤΩΦΛΙ ΣΚΗΝΗΣ** ή το
**ΕΛΑΧΙΣΤΟ ΜΕΓΕΘΟΣ ΑΝΘΡΩΠΟΥ**. Βλέπεις σημάδια πάνω σε ράφια; Ανέβασέ τα.

**6. Κάλυψε ό,τι δεν είναι κεφάλι.** Σύρε πάνω στην προεπισκόπηση:

- **Κάλυψη** — η περιοχή κρύβεται πάντα (πινακίδα, καρτελάκι ονόματος, οθόνη).
- **Εξαίρεση** — δεν ανιχνεύεται ποτέ. Βάλε μία πάνω στη σφραγίδα ημερομηνίας, που
  αλλιώς θεωρείται μόνιμη κίνηση.
- Δεξί κλικ σβήνει μια περιοχή.

**7. Διάλεξε εφέ και ένταση.** Δες την [ενότητα 4](#4-τα-πέντε-εφέ).

**8. `▶ Έναρξη επεξεργασίας`.** Μπορείς να ακυρώσεις όποτε θες· το μισοτελειωμένο
αρχείο σβήνεται.

**9. Έλεγξε τα ακάλυπτα καρέ.** Το κουμπί **Ακάλυπτα καρέ** σε πηγαίνει ένα ένα στα καρέ
όπου δεν βρέθηκε τίποτα. **Κάθε τέτοιο καρέ είναι πιθανή διαρροή.**

---

## 4. Τα πέντε εφέ

| Εφέ | Επηρεάζεται από την ένταση; | Τι κάνει |
|---|---|---|
| **Blur** | ναι | Γκαουσιανό θόλωμα. Το πιο διακριτικό — και το πιο επικίνδυνο σε χαμηλή ένταση. |
| **Mosaic** | ναι | Μεγάλα τετράγωνα μέσου χρώματος. |
| **Pixelate** | ναι | Υποδειγματοληψία και μεγέθυνση. |
| **Black Bars** | **όχι** | Συμπαγές μαύρο ορθογώνιο. Μη αναστρέψιμο σε κάθε ένταση. |
| **Circle** | **όχι** | Συμπαγής μαύρη έλλειψη, καλύπτει **93%** του κουτιού. Τα κεφάλια είναι στρογγυλά — ένα ορθογώνιο αφήνει τις γωνίες του να δείχνουν φόντο. |

### Ο δείκτης ασφάλειας

Κάτω από τη μπάρα έντασης υπάρχει μια γραμμή που υπολογίζει πόση πληροφορία επιβιώνει
στο μέγεθος προσώπου του συγκεκριμένου βίντεο. **Πράσινη** σημαίνει ότι έμειναν λίγα
«μπλοκ πληροφορίας»· **κίτρινη ή κόκκινη** σημαίνει ότι η ρύθμιση είναι πολύ αδύναμη.

Δεν είναι διακοσμητικό: σε χαμηλή ένταση, το Blur και το Pixelate αφήνουν αρκετή δομή
ώστε δημοσιευμένες τεχνικές αποθολώματος να ανακτήσουν αναγνωρίσιμο πρόσωπο.

> ⚠️ **Κανόνας:** αν το βίντεο πρόκειται να βγει από το χέρι σου — σε αστυνομία, σε
> ασφαλιστική, σε εκπαίδευση — χρησιμοποίησε **Circle** ή **Black Bars**. Δεν υπάρχει
> ένταση στην οποία να ανακτάται κάτι.

---

## 5. Κάθε χειριστήριο

### Λειτουργία «Πρόσωπα»

| Χειριστήριο | Προεπιλογή | Τι κάνει |
|---|---|---|
| Λίγα / Μερικά / Όλα | `Μερικά` | Πόσες αναλύσεις σαρώνονται. *Λίγα* = μόνο καθαρά κοντινά πρόσωπα, γρήγορο. *Όλα* = και μακρινά ή θολά, πιο αργό, με λίγα λάθη. |
| Ευαισθησία | `0.40` | Πόσο σίγουρος πρέπει να είναι ο ανιχνευτής. Χαμηλότερο = περισσότερα πρόσωπα και περισσότερα λάθη. |
| Σταθεροποίηση | `on` | Κρατά ένα πρόσωπο για λίγα καρέ αφού το χάσει ο ανιχνευτής. |

### Λειτουργία CCTV

| Χειριστήριο | Προεπ. / εύρος | Τι κάνει |
|---|---|---|
| **ΚΑΤΩΦΛΙ ΣΚΗΝΗΣ** | `30` · 20–90 | Πόσο πρέπει να διαφέρει ένα εικονοστοιχείο από την άδεια σκηνή για να μετρήσει ως άνθρωπος. **Χαμηλότερο πιάνει περισσότερους** — και περισσότερες σκιές. |
| **ΕΛΑΧΙΣΤΟ ΜΕΓΕΘΟΣ ΑΝΘΡΩΠΟΥ** | `250` · 80–3000 | Εξαρτάται από την κάμερα: αλλού ο άνθρωπος είναι ~100×300 px και αλλού ~25×70. Πολύ ψηλά, οι μακρινοί πετιούνται ως θόρυβος. |
| **Μόνο κεφάλι** | `on` | Καλύπτει μόνο το κεφάλι. Ξετσέκαρέ το για ολόκληρο τον άνθρωπο. |
| **ΥΨΟΣ ΚΕΦΑΛΙΟΥ** | `22%` · 8–60 | Το κεφάλι ως ποσοστό του σώματος. Εμφανίζεται όταν το «Μόνο κεφάλι» είναι τσεκαρισμένο. |
| **ΚΑΛΥΨΗ ΚΕΦΑΛΙΟΥ** | `55%` · 0–120 | Επεκτείνει κάθε άνθρωπο προς τα πάνω, γιατί σκούρα ρούχα σε σκούρο ράφι συχνά κόβουν το σχήμα στη μέση. Εμφανίζεται όταν το «Μόνο κεφάλι» είναι ξετσεκαρισμένο. |

> **Γιατί κατώφλι 30 και όχι 50;** Σε μέτρηση **1117 καρέ** πραγματικής κάμερας
> καταστήματος: με κατώφλι 50, στο **23% των καρέ δεν καλυπτόταν κανένας**. Με 40 έπεσε
> στο 6%. Με 30, σε κανένα. Μια χαμένη κεφαλή είναι αστοχία· ένα επιπλέον σημάδι σε ράφι
> είναι απλώς άσχημο καρέ.

### Οι υπόλοιπες επιλογές

- **Αφαίρεση ήχου** — προεπιλογή. Η φωνή ταυτοποιεί άνθρωπο, και ονόματα ή τηλέφωνα
  ακούγονται καθαρά.
- **Αναφορά επεξεργασίας** — γράφει αρχείο κειμένου δίπλα στην έξοδο: τι επεξεργάστηκε,
  πότε, με ποιες ρυθμίσεις, πόσα καρέ έμειναν ακάλυπτα. **Κράτα το: είναι η απόδειξη
  λογοδοσίας.**
- **Γλώσσα EN / GR** και **Θέμα Σκούρο / Φωτεινό** — διατηρούνται για την επόμενη φορά.
- **ΓΚΠΔ** — το πλήρες κείμενο συμμόρφωσης με ένα κλικ, μέσα στο πρόγραμμα.

---

## 6. Όταν κάτι πάει στραβά

### `cv2.error: Can't read ONNX file`

**Αιτία Α — χαλασμένο μοντέλο.** Ένα υγιές `face_detection_yunet_2023mar.onnx` είναι
περίπου **230 KB**. Αν είναι **131 bytes**, έχεις κατεβάσει έναν δείκτη Git LFS αντί για
το μοντέλο. Σβήσε το και ξανάνοιξε το πρόγραμμα.

**Αιτία Β — ελληνικά στη διαδρομή.** Το OpenCV ανοίγει το αρχείο με κωδικοποίηση των
Windows και δεν βλέπει ελληνικούς χαρακτήρες. Το AHA 1.3 το παρακάμπτει διαβάζοντας το
μοντέλο σε μνήμη· αν επιμείνει, βάλε το script σε διαδρομή με λατινικούς χαρακτήρες.

### `HOGDescriptor.detectMultiScale() got an unexpected keyword argument 'finalThreshold'`

Δεν προέρχεται από το AHA 1.3 — **το AHA δεν χρησιμοποιεί HOG**. Τρέχεις άλλη έκδοση.
Το OpenCV 5 κατάργησε αυτή την παράμετρο.

### Χάνονται άνθρωποι στη λειτουργία CCTV

Με τη σειρά: κατέβασε το **ΕΛΑΧΙΣΤΟ ΜΕΓΕΘΟΣ ΑΝΘΡΩΠΟΥ** (οι μακρινοί άνθρωποι είναι
μικροί), μετά κατέβασε το **ΚΑΤΩΦΛΙ ΣΚΗΝΗΣ**. Αν οι άνθρωποι είναι σε σκιά ή με ρούχα
στο χρώμα του ραφιού, το κατώφλι είναι το κρίσιμο.

### Σημάδια πάνω σε ράφια και στο πάτωμα

Ανέβασε το **ΕΛΑΧΙΣΤΟ ΜΕΓΕΘΟΣ ΑΝΘΡΩΠΟΥ**. Αν το σημάδι είναι πάντα στο ίδιο σημείο —
σφραγίδα ημερομηνίας, οθόνη, αυτόματη πόρτα — βάλε εκεί περιοχή **Εξαίρεση**.

### Ο ήχος δεν κρατήθηκε / τα μεταδεδομένα έμειναν

Λείπει το **ffmpeg** από το `PATH`. Η κάρτα ΕΠΙΛΟΓΕΣ σε προειδοποιεί όταν λείπει.

---

## 7. Το όριο που πρέπει να ξέρεις

> 🔴 **Ο ακίνητος άνθρωπος**
>
> Η εικόνα αναφοράς είναι η διάμεσος 60 καρέ. Άνθρωπος που στέκεται ακίνητος για
> **μεγάλο μέρος του βίντεο** υπάρχει στα περισσότερα δείγματα — άρα η διάμεσος τον
> θεωρεί μέρος του χώρου και **δεν ανιχνεύεται καθόλου**.
>
> Σε κλιπ 30 δευτερολέπτων με έναν πελάτη που στέκεται μπροστά σε έναν πάγκο, αυτό
> συμβαίνει. Σε εγγραφή ωρών από τα Duty Free, κάποιος που στέκεται πέντε λεπτά είναι
> ελάχιστο ποσοστό των δειγμάτων και πιάνεται κανονικά.
>
> **Τι κάνεις:** κόψε μεγαλύτερο κομμάτι βίντεο ώστε ο ακίνητος να είναι μειοψηφία, ή
> βάλε χειροκίνητη περιοχή **Κάλυψη** πάνω του.

Δύο ακόμη όρια, μικρότερα αλλά πραγματικά:

- Η λειτουργία CCTV θέλει **πραγματικά** ακίνητη κάμερα. Κάμερα που ταλαντεύεται στον
  αέρα ή κάνει pan κάνει ολόκληρο το κάδρο «κίνηση».
- **Το πρόσωπο δεν είναι το μόνο αναγνωριστικό.** Καρτελάκια ονόματος, πινακίδες
  οχημάτων, στολές, τατουάζ, βάδισμα — για αυτά υπάρχουν οι χειροκίνητες περιοχές.

---

## 8. ΓΚΠΔ

*Δεν αποτελεί νομική συμβουλή. Ένα πρόγραμμα δεν είναι ποτέ από μόνο του «συμβατό» —
συμβατή είναι η επεξεργασία.*

Το AHA είναι τεχνικό μέτρο κατά το **άρθρο 32**: βοηθά, δεν πιστοποιεί. Τρία σημεία που
παρεξηγούνται συχνά:

- **Το πρωτότυπο εξακολουθεί να υπάρχει.** Όσο υπάρχει, η έξοδος είναι
  *ψευδωνυμοποιημένη*, όχι ανώνυμη (**άρ. 4(5)**), και ο ΓΚΠΔ ισχύει πλήρως. Ανώνυμα
  είναι μόνο όσα δεν επιτρέπουν επαναταυτοποίηση με μέσα που ευλόγως ενδέχεται να
  χρησιμοποιηθούν (**αιτ. σκέψη 26**).
- **Δεν είναι επεξεργασία βιομετρικών του άρθρου 9.** Η ανίχνευση προσώπου χωρίς
  δημιουργία template ταυτοποίησης δεν συνιστά βιομετρική επεξεργασία —
  **EDPB Guidelines 3/2019** για συσκευές βίντεο.
- **Οι χαμένες ανιχνεύσεις είναι διαρροές.** Γι' αυτό υπάρχει το κουμπί «Ακάλυπτα καρέ»
  και η αναφορά επεξεργασίας (**λογοδοσία, άρ. 5(2)**).

### Λίστα ελέγχου πριν δώσεις το βίντεο

- [ ] Ο δείκτης ασφάλειας είναι πράσινος για κάθε μέγεθος κεφαλιού, ή χρησιμοποιείς Circle / Black Bars
- [ ] Ο ήχος αφαιρέθηκε, ή υπάρχει τεκμηριωμένος λόγος να κρατηθεί
- [ ] Πινακίδες, καρτελάκια και οθόνες καλύφθηκαν με χειροκίνητες περιοχές
- [ ] Τα ακάλυπτα καρέ ελέγχθηκαν ένα προς ένα
- [ ] Το πρωτότυπο φυλάσσεται ασφαλώς ή διαγράφηκε κατά την πολιτική διατήρησης
- [ ] Η αναφορά επεξεργασίας κρατήθηκε μαζί με το αρχείο εξόδου

---
---

# English

## Contents

1. [What the program does](#1-what-the-program-does)
2. [What it needs to run](#2-what-it-needs-to-run)
3. [The procedure, step by step](#3-the-procedure-step-by-step)
4. [The five effects](#4-the-five-effects)
5. [Every control](#5-every-control)
6. [When something goes wrong](#6-when-something-goes-wrong)
7. [The limit you must know about](#7-the-limit-you-must-know-about)
8. [GDPR](#8-gdpr)

---

## 1. What the program does

AHA is a desktop tool, not a service. It runs entirely on your own machine: no frame
leaves the computer, there is no telemetry, and no log with personal data is kept. The
only time it touches the network is on the very first run, if the detection model file is
missing — it downloads it from GitHub and sends nothing.

The original is left untouched: AHA always writes a **new** file, named after the original
with the effect appended (`TestFile_BlackBars.mp4`).

It performs face **detection**, not face **recognition**. No biometric template is ever
built or stored; it finds where a head is and erases it.

### Two different problems, two modes

| | **Faces (any video)** | **CCTV (fixed camera)** |
|---|---|---|
| How it works | A face detector examines every frame on its own | Learns the **empty** space first; anything that differs is a person |
| Material | Anything — handheld, phone, drone | Requires a **motionless** camera |
| Person from behind | ❌ not caught | ✅ caught |
| Needs a visible face | yes | no |

> **For the Duty Free:** use **CCTV** mode. The cameras are fixed and high, people move
> among shelves that hide their bodies, and very often never look at the camera — exactly
> the case where a face detector is not enough.

---

## 2. What it needs to run

| What | Detail | If missing |
|---|---|---|
| Python | `3.9+` | The program will not open. |
| OpenCV | `pip install opencv-python` | Tested on 5.0.0. |
| NumPy | `pip install numpy` | Comes along with OpenCV. |
| Model | `face_detection_yunet_2023mar.onnx` | Downloads itself next to the script. |
| ffmpeg | on `PATH` | Optional. Without it audio cannot be kept and metadata is not stripped. |

Settings are written to `aha_settings.json`, next to `AHA_1.3.py`. Delete that file to
start again from the defaults.

```
python AHA_1.3.py
```

The window opens maximized.

---

## 3. The procedure, step by step

The order matters: **the reference must be built before the preview means anything.**

**1. Choose the video.** `Open...` next to **Input**. The **Output** field fills itself:
*the input name plus the effect*.

```
Input:   F:/Videos/TestFile.mp4
Output:  F:/Videos/TestFile_BlackBars.mp4
```

Change the effect and the name changes with it, so two exports of the same clip never
overwrite each other. Type or pick a name of your own and the program leaves it alone
from then on.

**2. Choose the mode.** In the **DETECTION** card: *Faces (any video)* or
*CCTV (fixed camera)*.

**3. CCTV only: build the reference.** `Build reference` samples 60 frames from across the
video and keeps the median value of every pixel; people, being transient, disappear and
the empty shop is left behind. It takes a few seconds. **Without it, CCTV mode detects
nothing.**

**4. Check in the preview.** Drag the **Frame** bar to several points. Tick **Detection
boxes** to see exactly where the marks land. *This is the step that decides the result —
do not skip it.*

**5. Adjust what needs adjusting.** Are people being missed? Lower **SCENE THRESHOLD** or
**MINIMUM PERSON SIZE**. Seeing marks on shelves? Raise them.

**6. Cover what is not a head.** Drag on the preview:

- **Cover** — the area is always hidden (a plate, a name badge, a monitor).
- **Exclude** — it is never detected. Put one over the timestamp, which otherwise counts
  as permanent motion.
- Right-click removes an area.

**7. Choose effect and strength.** See [section 4](#4-the-five-effects).

**8. `▶ Start processing`.** You can cancel at any point; the half-written file is
deleted.

**9. Review the uncovered frames.** The **Uncovered frames** button walks you one by one
through the frames where nothing was found. **Every such frame is a potential
disclosure.**

---

## 4. The five effects

| Effect | Strength applies? | What it does |
|---|---|---|
| **Blur** | yes | Gaussian blur. The most discreet — and the most dangerous at low strength. |
| **Mosaic** | yes | Large squares of average colour. |
| **Pixelate** | yes | Downsample and enlarge. |
| **Black Bars** | **no** | Solid black rectangle. Irreversible at every strength. |
| **Circle** | **no** | Solid black ellipse covering **93%** of the box. Heads are round — a rectangle leaves its corners showing background. |

### The safety indicator

Under the strength slider is a line that computes how much information survives at the
face size of this particular video. **Green** means few "residual blocks" are left;
**amber or red** means the setting is too weak.

This is not decorative: at low strength, Blur and Pixelate leave enough structure that
published de-blurring techniques can recover a recognisable face.

> ⚠️ **Rule of thumb:** if the video is going to leave your hands — to the police, to an
> insurer, into training material — use **Circle** or **Black Bars**. There is no strength
> setting at which anything can be recovered from them.

---

## 5. Every control

### Faces mode

| Control | Default | What it does |
|---|---|---|
| Few / Some / All | `Some` | How many scales are scanned. *Few* = only clear, close faces, fastest. *All* = also distant or blurry ones, slower, with a few false hits. |
| Sensitivity | `0.40` | How sure the detector must be. Lower = more faces and more false hits. |
| Stabilize | `on` | Holds a face for a few frames after the detector loses it. |

### CCTV mode

| Control | Default / range | What it does |
|---|---|---|
| **SCENE THRESHOLD** | `30` · 20–90 | How far a pixel must differ from the empty scene to count as a person. **Lower catches more people** — and more shadows. |
| **MINIMUM PERSON SIZE** | `250` · 80–3000 | Camera dependent: a person is ~100×300 px in one view and ~25×70 in another. Too high and distant people are dropped as noise. |
| **Head only** | `on` | Covers just the head. Uncheck to cover the whole person. |
| **HEAD HEIGHT** | `22%` · 8–60 | The head as a share of the body. Shown when "Head only" is ticked. |
| **HEAD COVERAGE** | `55%` · 0–120 | Extends each person upward, because dark clothing on dark shelving often cuts the shape at the waist. Shown when "Head only" is unticked. |

> **Why threshold 30 and not 50?** Measured over **1117 frames** of a real shop camera: at
> threshold 50, **23% of frames had nobody covered at all**. At 40 it fell to 6%. At 30,
> none. A missed head is a failure; an extra mark on a shelf is only an ugly frame.

### The remaining options

- **Remove audio** — the default. A voice identifies a person, and names or phone numbers
  stay perfectly audible.
- **Processing report** — writes a text file next to the output recording what was
  processed, when, with which settings, and how many frames were left uncovered.
  **Keep it: it is your accountability record.**
- **Language EN / GR** and **Theme Dark / Light** — both remembered for next time.
- **GDPR** — the full compliance text inside the program, one click away.

---

## 6. When something goes wrong

### `cv2.error: Can't read ONNX file`

**Cause A — a broken model file.** A healthy `face_detection_yunet_2023mar.onnx` is about
**230 KB**. If it is **131 bytes**, you have a Git LFS pointer instead of the model.
Delete it and reopen the program.

**Cause B — Greek characters in the path.** OpenCV opens the file through the Windows
codepage and cannot see them. AHA 1.3 works around this by loading the model into memory;
if it still fails, put the script in a path with Latin characters only.

### `HOGDescriptor.detectMultiScale() got an unexpected keyword argument 'finalThreshold'`

This does not come from AHA 1.3 — **AHA does not use HOG**. You are running a different
build. OpenCV 5 removed that parameter.

### People are missed in CCTV mode

In order: lower **MINIMUM PERSON SIZE** (distant people are small), then lower **SCENE
THRESHOLD**. If people are in shadow or wearing the colour of the shelving, the threshold
is the one that matters.

### Marks landing on shelves and on the floor

Raise **MINIMUM PERSON SIZE**. If the mark is always in the same place — a timestamp, a
monitor, an automatic door — put an **Exclude** area over it.

### Audio was not kept / metadata survived

**ffmpeg** is not on the `PATH`. The OPTIONS card warns you when it is missing.

---

## 7. The limit you must know about

> 🔴 **The person who stands still**
>
> The reference image is the median of 60 frames. Someone who stands motionless for **a
> large share of the video** is present in most of those samples — so the median treats
> them as part of the room and **they are never detected at all**.
>
> In a 30-second clip with one customer standing at a counter, this happens. In an
> hours-long Duty Free recording, someone standing for five minutes is a tiny fraction of
> the samples and is caught normally.
>
> **What to do:** take a longer stretch of video so the motionless person is a minority,
> or put a manual **Cover** area over them.

Two further limits, smaller but real:

- CCTV mode needs a **genuinely** motionless camera. One that sways in the air, or pans,
  makes the whole frame read as motion.
- **A face is not the only identifier.** Name badges, vehicle plates, uniforms, tattoos,
  gait — that is what the manual areas are for.

---

## 8. GDPR

*This is not legal advice. A program is never "compliant" on its own — the processing is
what is compliant.*

AHA is a technical measure under **Article 32**: it helps, it does not certify. Three
points that are commonly misread:

- **The original still exists.** While it does, the output is *pseudonymized*, not
  anonymous (**Art. 4(5)**), and the GDPR applies in full. Data is anonymous only when no
  one can re-identify it by means reasonably likely to be used (**Recital 26**).
- **This is not Article 9 biometric processing.** Face detection without building an
  identification template does not constitute biometric processing — **EDPB Guidelines
  3/2019** on video devices.
- **Missed detections are disclosures.** That is why the "Uncovered frames" button and the
  processing report exist (**accountability, Art. 5(2)**).

### Checklist before you hand the video over

- [ ] The safety indicator is green for every head size, or you are using Circle / Black Bars
- [ ] Audio was removed, or there is a documented reason to keep it
- [ ] Plates, badges and screens are covered with manual areas
- [ ] The uncovered frames were reviewed one by one
- [ ] The original is stored securely or deleted per your retention policy
- [ ] The processing report is kept alongside the output file

---

<sub>AHA 1.3 · Automatic Head Anonymization · ™ 2026 · © Copyright by Petros Andrianos</sub>
