# Guide Pas à Pas : Développement du Plugin Obsidian "Mistral Note Enricher"

## **1. Prérequis**
- Node.js (v16+)
- Git
- Un vault Obsidian pour les tests
- Une clé API Mistral (à configurer dans les paramètres du plugin)

---

## **2. Initialisation du Projet**

### **A. Créer le projet**
```bash
# Cloner le template officiel Obsidian
git clone https://github.com/obsidian-sample-plugins/plugin-starter mistral-note-enricher
cd mistral-note-enricher

# Installer les dépendances
npm install axios
npm install @types/node --save-dev
```

### **B. Renommer le plugin**
- Modifier `manifest.json` :
  ```json
  {
    "id": "mistral-note-enricher",
    "name": "Mistral Note Enricher",
    "version": "1.0.0",
    "minAppVersion": "1.0.0",
    "description": "Enrichit les métadonnées et liens des notes via l'API Mistral.",
    "author": "Emmanuel Conrardy",
    "authorUrl": "",
    "isDesktopOnly": true
  }
  ```

---

## **3. Structure des Fichiers**
```
mistral-note-enricher/
├── main.ts          # Logique principale
├── modal.ts         # Interface de validation
├── settings.ts      # Paramètres (clé API, dossiers, fréquence)
├── git.ts           # Gestion des commits Git
├── styles.css       # Styles pour la modal
└── manifest.json
```

---

## **4. Développement Étape par Étape**

### **A. `settings.ts` : Gestion des Paramètres**
```typescript
interface EnricherSettings {
    apiKey: string;
    sourceFolder: string;
    targetFolder: string;
    scanInterval: number;
}

export default class EnricherSettingsTab extends PluginSettingTab {
    plugin: MistralNoteEnricher;

    constructor(app: App, plugin: MistralNoteEnricher) {
        super(app, plugin);
        this.plugin = plugin;
    }

    display(): void {
        const { containerEl } = this;
        containerEl.empty();

        new Setting(containerEl)
            .setName("Clé API Mistral")
            .setDesc("Ta clé API pour les appels à Mistral.")
            .addText(text => text
                .setPlaceholder("sk-...")
                .setValue(this.plugin.settings.apiKey)
                .onChange(async (value) => {
                    this.plugin.settings.apiKey = value;
                    await this.plugin.saveSettings();
                }));

        // Ajouter les autres paramètres (dossiers, intervalle)
    }
}
```

---

### **B. `main.ts` : Logique Principale**
#### **1. Charger les paramètres**
```typescript
export default class MistralNoteEnricher extends Plugin {
    settings: EnricherSettings;

    async onload() {
        await this.loadSettings();
        this.addSettingTab(new EnricherSettingsTab(this.app, this));

        // Déclencher à l'ouverture
        this.registerEvent(this.app.workspace.on("layout-ready", () => this.scanNotes()));

        // Commande manuelle
        this.addCommand({
            id: "enrich-active-note",
            name: "Enrichir la note active",
            callback: () => this.enrichActiveNote(),
        });

        // Scan périodique
        this.registerInterval(
            window.setInterval(() => this.scanNotes(), this.settings.scanInterval * 60000)
        );
    }

    async loadSettings() {
        this.settings = Object.assign({}, DEFAULT_SETTINGS, await this.loadData());
    }

    async saveSettings() {
        await this.saveData(this.settings);
    }
}
```

#### **2. Scanner les notes éligibles**
```typescript
async function scanNotes() {
    const files = this.app.vault.getMarkdownFiles();
    const sourcePath = `${this.app.vault.adapter.basePath}/${this.settings.sourceFolder}`;

    for (const file of files) {
        if (file.path.startsWith(sourcePath) && !file.frontmatter?.enriched) {
            const content = await this.app.vault.read(file);
            const existingTitles = await this.getExistingNoteTitles();
            const enrichedData = await this.enrichNote(content, existingTitles);
            this.showValidationModal(file, enrichedData);
        }
    }
}
```

#### **3. Appeler l'API Mistral**
```typescript
async function enrichNote(content: string, existingTitles: string[]): Promise<EnrichedData> {
    const prompt = `
        Analyse la note et propose :
        1. 3 tags (1-2 mots, minuscules)
        2. Un résumé en une phrase
        3. 2 liens parmi : ${existingTitles.join(", ")}.
        Note : ${content}
    `;

    const response = await axios.post(
        "https://api.mistral.ai/v1/chat/completions",
        {
            model: "mistral-tiny",
            messages: [{ role: "user", content: prompt }],
        },
        { headers: { "Authorization": `Bearer ${this.settings.apiKey}` } }
    );

    return this.parseMistralResponse(response.data);
}

function parseMistralResponse(data: any): EnrichedData {
    // Extraire tags, summary, links depuis data.choices[0].message.content
    // Exemple : { tags: ["tag1", "tag2"], summary: "...", links: ["[[Note 1]]", "[[Note 2]]"] }
}
```

#### **4. Afficher la modal de validation**
```typescript
function showValidationModal(file: TFile, enrichedData: EnrichedData) {
    new EnricherModal(this.app, enrichedData, async (confirmed: boolean) => {
        if (confirmed) {
            await this.saveEnrichedNote(file, enrichedData);
        }
    }).open();
}
```

#### **5. Sauvegarder et commiter la note enrichie**
```typescript
async function saveEnrichedNote(file: TFile, data: EnrichedData) {
    const targetPath = this.getSafeFilename(this.settings.targetFolder, file.basename);
    const frontmatter = `
        ---
        tags: [${data.tags.join(", ")}]
        summary: "${data.summary}"
        links: ${data.links.join(", ")}
        source: ${file.path}
        enriched: true
        ---
    `;
    const newContent = frontmatter + "\n\n" + (await this.app.vault.read(file));

    await this.app.vault.create(targetPath, newContent);
    await this.commitToGit(targetPath, `Enrichissement : ${file.basename}`);
}
```

---

### **C. `modal.ts` : Interface de Validation**
```typescript
export class EnricherModal extends Modal {
    constructor(app: App, data: EnrichedData, onSubmit: (confirmed: boolean) => void) {
        super(app);
        this.onSubmit = onSubmit;
        this.data = data;
    }

    onOpen() {
        const { contentEl } = this;
        contentEl.createEl("h2", { text: "Valider l'enrichissement" });

        // Afficher les données enrichies
        contentEl.createEl("p", { text: `Tags : ${this.data.tags.join(", ")}` });
        contentEl.createEl("p", { text: `Résumé : ${this.data.summary}` });
        contentEl.createEl("p", { text: `Liens : ${this.data.links.join(", ")}` });

        // Boutons
        new Setting(contentEl)
            .addButton(btn => btn
                .setButtonText("Valider")
                .onClick(() => { this.close(); this.onSubmit(true); }))
            .addButton(btn => btn
                .setButtonText("Ignorer")
                .onClick(() => { this.close(); this.onSubmit(false); }));
    }
}
```

---

### **D. `git.ts` : Gestion des Commits**
```typescript
import { exec } from "child_process";
import { promisify } from "util";
const execAsync = promisify(exec);

async function commitToGit(filePath: string, message: string) {
    const gitPath = this.app.vault.adapter.basePath;
    try {
        await execAsync(`cd ${gitPath} && git add ${filePath} && git commit -m "${message}" && git push`);
    } catch (error) {
        console.error("Erreur Git :", error);
    }
}
```

---

## **5. Tests & Validation**

### **A. Préparer l'environnement de test**
1. Créer un dossier `03-research-inbox` dans ton vault.
2. Ajouter 2-3 notes Markdown (sans frontmatter `enriched`).
3. Configurer la clé API Mistral dans les paramètres du plugin.

### **B. Tester les fonctionnalités**
1. **Scan automatique** :
   - Ouvrir Obsidian → Le plugin doit scanner `03-research-inbox`.
2. **Validation manuelle** :
   - Une modal doit s’ouvrir pour chaque note éligible.
3. **Vérifier le résultat** :
   - Les notes enrichies doivent apparaître dans `04-enriched-notes`.
   - Les commits doivent être visibles dans l’historique Git.

---

## **6. Débogage**
- **Erreurs courantes** :
  - `401 Unauthorized` : Vérifier la clé API Mistral.
  - `File already exists` : Vérifier la fonction `getSafeFilename`.
  - `Git not found` : Installer Git et ajouter au PATH.

- **Logs** :
  ```typescript
  console.log("Début du scan...");
  console.log("Réponse API :", response.data);
  ```

---

## **7. Déploiement**
1. Builder le plugin :
   ```bash
   npm run build
   ```
2. Copier le dossier dans `.obsidian/plugins/` de ton vault.
3. Activer le plugin dans Obsidian.

---

## **8. Améliorations Futures (V2)**
- Gestion des conflits de fusion Git.
- Support des PDFs (OCR + analyse).
- Logs détaillés des enrichissements.
- Interface pour éditer les prompts API.