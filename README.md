# 3η Εργασία Arch_Lab
## Βήμα 1o. Εξοικείωση με τον εξομοιωτή McPAT
### **1]**
 * **Dynamic Power**:
>Circuits dissipate **dynamic power** when they charge and discharge the capacitive loads to switch states. Dynamic power is **proportional to** the total **load capacitance**, the **supply voltage**, the **voltage swing during switching**, the **clock frequency**, and the **activity factor**.

Όπως υπογραμμίζω παραπάνω, **dynamic power** είναι η ισχύς που καταναλώνεται για την φόρτιση και αποφόρτιση των χωρητικών φορτίων του επεξεργαστή. Έχει ανάλογη εξάρτηση με τη **συνολική χωρητικότητα**, την **τάση τροφοδοσίας**, τη **μεταβολή της τάσης κατά την φόρτιση και αποφόρτιση**, την **συχνότητα ρολογιού** και του **activity factor**, ενός παράγοντα που αποτελεί την πιθανότητα αλλαγής κατάστασης από 1 σε 0, ή αντίστροφα, σε ένα δεδομένο κύκλο του ρολογιού.

 * **Power Leakage**: 
 >**Transistors** in circuits **leak**, dissipating static power. **Leakage current depends on** the **width of the transistors** and the **local state of the devices**. There are two leakage mechanisms. **Subthreshold leakage** occurs because a small current passes between the source and drain of off-state transistors. **Gate leakage** is the current leaking through the gate terminal, and varies greatly with the state of the device.

Όπως, πάλι, φαίνετια στο documentation, **power leakage** είναι η συνολική απώλεια ενέργειας  σε καθε τρανζίστορ, καθώς, προφανώς, τίποτα δεν μπορεί να είναι ιδανικό. Υπάρχουν τα **Subthreshold και Gate** leakage αντίστοιχα, το πρώτο οφείλεται στη διαρροή ρεύματος από την πηγή του off-state τρανζίστορ στην εκροή και το δεύτερο, αυτολεξεί, στη διαρροή ρεύματος από το **gate** των τρανζίστορ, εξαρτόμενο από την κατάσταση του.

Παρατηρούμε ότι κανένα από τα παραπάνω χαρακτηριστικά **δεν εξαρτάται** επ'ουδενί λόγω από τον **χρόνο εκτέλεσης του προγράμματος**, ωστόσο επηρεάζονται από την κατάσταση των τρανζίστορ και επομένως, το εκάστωτε πρόγραμμα που τα χρησιμοποιεί. 

[According to McPAT Documentation](https://www.hpl.hp.com/research/mcpat/micro09.pdf)


### **2]** 

**Δεν γίνεται να γνωρίζουμε τη διάρκεια μπαταρίας**, γνωρίζοντας μόνο την ισχύ των επεξεργαστών, εφόσον μόλις καταλήξαμε στο συμπέρασμα ότι πολλές χαρακτηριστικές τιμές αλλάζουν ανάλογα με την αρχιτεκτονική των επεξεργαστών, τις ρυθμίσεις και τα προγράμματα που εκτελούνται. Πρέπει να ξέρουμε πολλά παραπάνω απ'όσα προσφέρει ο McPAT για να βγάλουμε συμπέρασμα για το efficiency των επεξεργαστών (χρόνος εκτέλεσης προγράμματος, τύπος προγράμματος, αρχιτεκτονική και παράμετροι των επεξεργαστών κλπ).


### **3]** 

Από το **McPAT** για τα **Xeon** και **ARM A9** προκύπτουν τα εξής:
 * **Xeon**:
	 * **Runtime Dynamic Power**: **72,9199 W**
	 * **Total Leakage** (**Subthreshold Leakage** + **Gate Leakage**): **36,8319 W**
 * **ARM A9**:
	 * **Runtime Dynamic Power**: **2,96053 W**
	 * **Total Leakage** (**Subthreshold Leakage** + **Gate Leakage**): **0,108687 W**

Είναι οφθαλμοφανές ότι υπάρχει τεράστια διαφορά στις τιμές που παίρνουμε από το McPAT. Η ενέργεια θα υπολογισθεί ως η συνολική ισχύς που καταναλώνει το καθένα, επί τον χρόνο για τον οποίο την καταναλώνει (έστω **1s ο Xeon** και **50s o ARM A9**). Άρα:

 * **Xeon**: **109,7518 W** * 1s = **109,7518 J**
 * **ARM A9**: **3,069217 W** * 50s = **153.46085 J**

Βλέπουμε ότι ο **Xeon** φαίνεται να καταναλώνει **λιγότερη** ενέργεια συνολικά, ωστόσο με την παράμετρο του προβλήματος, ότι μετά την εκτέλεση ο **Xeon** δεν θα τερμάτισει τη λειτουργία του, σημαίνει ότι για τα υπόλοιπα 49 δευτερόλεπτα που ο **ARM A9** τρέχει το πρόγραμμα, ο **Xeon** θα συνεχίζει να χάνει ενέργεια μέσω διαρροής (**leakage**). Eπομένως εν προκειμένω, **δεν γίνεται ο Xeon να είναι overall πιο energy efficient**. 

## Βήμα 2ο. gem5 + McPAT: αναζητώντας τη βελτιστοποίηση του γινομένου EDP

### **1]**

Χρησιμοποιώντας το ***print_energy.sh*** έλαβα τις παρακάτω τιμές για την ενέργεια του κάθε benchmark:
| Energy Consumption (milliJoules) | opt_1      | opt_2      | opt_3      | opt_4      | opt_5      |
|----------------------------------|------------|------------|------------|------------|------------|
| **429**                              | 111,100944 | 111,352051 | 95,536459  | 79,624119  | 76,711287  |
| **458**                              | 602,711455 | 600,306261 | 880,763499 | 826,654363 | 826,654363 |
| **470**                              | 212,483640 | 260,322987 | 308,382699 | 237,660157 | 213,438275 |

Έπειτα θα κάνω τον πολλαπλασιασμό του **Energy Consumption** με το **runtime**, το οποίο έιναι το **delay** στο **Energy-Delay Product**, βγαλμένο επίσης μέσω του ***print_energy.sh***. Τα αποτελέσματα έχουν ως εξής:

| Energy-Delay Product (milliJoules * seconds) | opt_1    | opt_2    | opt_3    | opt_4    | opt_5    |
|----------------------------------------------|----------|----------|----------|----------|----------|
| 429                                          | 6,4194   | 6,4595   | 5,5193   | 4,5985   | **4,4307**   |
| 458                                          | 309,5031 | **308,3491** | 452,2931 | 729,6754 | 729,6754 |
| 470                                          | 37,1134  | 45,4693  | 53,8655  | 41,5123  | **27,5474**  |

Όπως φαίνεται με **bold text** στον πίνακα, τα βέλτιστα ως προς το **Energy-Delay Product** optimizations ήταν τα:
 * **429_opt_5**:  
	 *  L1 Data Cache Size = 64kB
	 *  L1 Instruction Cache Size = 32kB
	 *  L2 Cache Size = 2048kB
	 *  L1 Data Cache Associativity =4
	 *  L1 Instruction Cache Associativity =4
	 *  Cacheline Size = 64
 * **458_opt_2**:  
	 *  L1 Data Cache Size = 64kB
	 *  L1 Instruction Cache Size = 64kB
	 *  L2 Cache Size = 1024kB
	 *  L1 Data Cache Associativity = 4
	 *  L1 Instruction Cache Associativity = 4
	 *  Cacheline Size = 64
 * **470_opt_5**:  
	 *  L1 Data Cache Size = 64kB
	 *  L1 Instruction Cache Size = 32kB
	 *  L2 Cache Size = 2048kB
	 *  L1 Data Cache Associativity = 2
	 *  L1 Instruction Cache Associativity = 2
	 *  Cacheline Size = 128

### **2]**
	
Παρακάτω υπάρχουν γραφήματα για κάθε benchmark:

[![HokQF94.md.png](https://iili.io/HokQF94.md.png)](https://freeimage.host/i/HokQF94)

[![HokQ2Sf.md.png](https://iili.io/HokQ2Sf.md.png)](https://freeimage.host/i/HokQ2Sf)

 [	 ![HokQdcG.md.png](https://iili.io/HokQdcG.md.png)](https://freeimage.host/i/HokQdcG)

Παρατηρούμε ότι η αλλαγή του **Cacheline size** επιφέρει σημαντική διαφορά στο **Peak Power Consumption**.

### **3]**




### Παρατηρήσεις για την αξιοπιστία και ορθότητα των συμπερασμάτων: 
Θεωρώ ότι τα συμπεράσματα που έχω βγάλει από τα παραπάνω είναι ορθά μονό για πολύ προσεγγιστική εκτίμηση των διαφορών σε αρχιτεκτονικές/ρυθμίσεις/προγράμματα.
Θεωρώ ότι η αναξιοπιστία που έχει προκύψει από: 
 * μικρό δείγμα βελτιστοποιήσεων,
 *  προσωπικού bias ως προς τις ρυθμίσεις κάθε benchmark βελτιστοποίησης (για το μικρό αυτό δείγμα),
 *  αλλά και το γεγονός ότι ενας simulator δεν γίνεται να αντιστοιχεί ακριβώς στην πραγματικότητα, πόσο μάλλον όταν χρησιμοποιώ και δεύτερο πρόγραμμα που μπορεί να πάρει τα δικά του σφάλματα και να τα αλλάξει παιρετάιρω για να μπορεί να λειτουργήσει (όπως κάνει ο McPAT με πληροφορίες που παίρνει από τον gem5), 
 * ενώ παράλληλα τα παραπάνω προσπαθούν να εξομοιώσουν λειτουργίες hardware υλικού μέσω high-level software, το οποίο σίγουρα αποκλίνει από την πραγματικότητα,

καθιστά τα συμπεράσματα που έχω βγάλει πολύ επικίνδυνα για την ακριβή εκτίμηση της λειτουργίας όσων έχω εξετάσει.

### Sources:
 * [**HP Labs**: McPAT documentation](https://www.hpl.hp.com/research/mcpat/)
	 * [Extended documentation on **McPAT**, useful information on **dynamic power** and **power leakage**](https://www.hpl.hp.com/research/mcpat/micro09.pdf)
 * [Microsoft **Excel** (used for graphs)](https://www.microsoft.com/el-gr/microsoft-365/excel)
 * [**StackEdit** used for creating **Markdown** files](https://stackedit.io/)
 * [**Freeimage.host** for online **image hosting** (used for embedding images in MarkDown files)](https://freeimage.host/)
 * [**Tables Generator** for formatting **tables in Markdown format** and copying to clipboard for pasting in MarkDown file](https://tablesgenerator.com/markdown_tables#)


### Κριτική της 3ης εργασίας Αρχιτεκτονικής Προηγμένων Υπολογιστών:
Πιο απλή θεωρώ στην υλοποίηση από την 2η και σίγουρα λιγότερο χρονοβόρα, περισσότερο χρόνο ξόδεψα στο να ξαναυλοποιώ ερωτήματα της 2ης εργασίας επειδή ο τρόπος που τα είχα υλοποιήσει δεν βοηθούσε. Ενδιαφέρον dynamic η διαμεσολάβηση script για να περαστεί πληροφορία από τον gem5 στο McPAT. Προσωπικά, δεν κατάλαβα πολύ γιατί ζητήθηκε να κατεβάσουμε το my_mcpat ενώ υπήρχε ήδη το mcpat, σπατάλησα τις πρωτες 5 ημέρες αφότου άνοιξε η εργασία προσπαθώντας και νομίζοντας ότι δε μπορώ να την κάνω επειδή δεν μπορούσα να φτιάξω τα dependencies για να δουλέψει το makefile που ζητείται, και εν τέλει ήταν όλα ήδη εγκατεστημένα εξ αρχής στο mcpat (στο VM) (φαντάζομαι αναφερόταν σε όσους δε δούλευαν με το VM που μας δόθηκε; Όπως και νά'χει δεν ήταν πολύ σαφές). Σίγουρα πολύ ενδιαφέρον, ιδίως το 3ο εργαστήριο που πηγαίνεις από πληροφορίες όπως CPIs και cache misses σε πιο απτές και αισθητές πληροφορίες όπως ισχύς και ενέργεια. Σημαντικό insight για το πως δουλεύουν κάποια πράγματα, που θεωρώ θα φανεί χρήσιμο και χωρίς να θελώ specifically να εμβαθύνω πολύ περισσότερο στην Αρχιτεκτονική Υπολογιστών στο μέλλον. 
