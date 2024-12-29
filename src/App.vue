<template>
  <v-app>
    <!-- Header -->
    <v-app-bar flat app class="custom-header elevation-1">
      <v-container>
        <v-row align="center" justify="space-between" class="header-row">
          <!-- Left: Main Logo -->
          <v-img
            src="./assets/neuro-comparison.png"
            alt="Main Logo"
            contain
            max-height="60"
            class="main-logo"
          >TRAIN YOUR PROMPTS</v-img>

          <!-- Center: Option Pills -->
          <v-row align="center" justify="center" class="option-pills">
           <v-btn
            v-for="(pill, index) in pills"
            :key="index"
            class="pill-button mx-1"
            outlined
            large
            :class="{ 'pill-selected': activePanels.includes(index) }"
            @click="openDialog(index)"
            >
          <v-row align="center" justify="center" class="pill-content">
            <v-img
              :src="pill.logo"
              :alt="pill.name"
              contain
              max-height="22"
              class="mr-2"
            ></v-img>
    <span class="pill-text">{{ pill.name }}</span>
  </v-row>
</v-btn>
          </v-row>

          <!-- Right: Add APIs Dropdown -->
          <v-menu offset-y>
            <template #activator="{ on, attrs }">
              <v-btn text small v-bind="attrs" v-on="on" class="add-apis-btn">
                <v-icon class="mr-1">mdi-plus</v-icon>
              </v-btn>
            </template>
            <v-list>
              <v-list-item v-for="(option, index) in additionalOptions" :key="index" @click="handleDropdown(option)">
                <v-list-item-title>{{ option }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>
        </v-row>
      </v-container>
    </v-app-bar>

    <!-- Main Content -->
    <v-container class="main-content">
      <v-row align="center" justify="center">
        <!-- Panels Section -->
        <v-row dense class="center-panels">
          <v-col cols="12" md="6" v-for="panelIndex in activePanels" :key="panelIndex">
            <v-card outlined class="square-panel">
              <v-card-title class="panel-header">
                {{ pills[panelIndex].name }}
                <v-btn
                  icon
                  small
                  class="remove-btn"
                  @click="removePanel(panelIndex)"
                >
                  <v-icon>mdi-close-circle</v-icon>
                </v-btn>
              </v-card-title>
              <v-card-text>
                <div class="panel-content">
                  {{ panels[panelIndex].content }}
                </div>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>

        <!-- Import Text Panel -->
        <v-col cols="12" class="mt-3">
          <v-card outlined class="import-panel">
            <v-card-text>
              <v-row align="center" class="import-row">
                <!-- Input Field -->
                <v-text-field
                  v-model="importText"
                  label="Start typing or paste"
                  outlined
                  class="flex-grow-1"
                ></v-text-field>
                

                <!-- Plus Button for Upload 
                <v-btn icon small class="upload-btn" @click="openFileBrowser">
                  <v-icon>mdi-plus</v-icon>
                </v-btn>-->

                <!-- Compare Button -->
                <v-btn color="secondary" class="compare-btn" @click="compareText">
                  Compare AI
                </v-btn>
              </v-row>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>

        <v-container class="app-description">
          <v-row justify="center">
            <v-col cols="12" md="8">
              <v-textarea
                v-model="displayedText"
                label="About This App"
                
                readonly
                auto-grow
                class="description-textarea"
              ></v-textarea>
            </v-col>
          </v-row>
        </v-container>

      </v-container>
   

    <!-- Add APIs Dialog -->
    <v-dialog v-model="dialog" max-width="580">
      <v-card>
        <v-card-title>Select AI API</v-card-title>

        <!-- Pills in Pop-Up -->
        <v-row class="mb-3 poppils" align="center" justify="start">
          <v-btn
            v-for="(pill, index) in pills"
            :key="index"
            class="pill-button mx-1"
            outlined
            small
            :class="{ 'pill-selected': selectedPill === index }"
            @click="selectPill(index)"
          >
            <v-img
              :src="pill.logo"
              :alt="pill.name"
              contain
              max-height="20"
              max-width="20"
              class="mr-2"
            ></v-img>
            {{ pill.name }}
          </v-btn>
        </v-row>

        <!-- Input Fields -->
        <v-card-text>
          <v-form ref="form">
            <v-text-field label="API http Endpoint" v-model="apiEndpoint" required></v-text-field>
            <v-text-field label="API Secret" v-model="apiName" required></v-text-field>
          </v-form>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="addPanel">Add</v-btn>
          <v-btn text @click="dialog = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      dialog: false,
      apiName: '',
      apiEndpoint: '',
      panels: [
        { content: 'Generated result' },
        { content: 'Generated result' },
        { content: 'Generated result' },
        { content: 'Generated result' },
        { content: 'Generated result' },
        { content: 'Generated result' },
        { content: 'Generated result' },
        { content: 'Generated result' },
        { content: 'Generated result' },
      ],
      pills: [
        { name: 'openAi', logo: require('./assets/openai.webp') },
        { name: 'Gemini', logo: require('./assets/gemini.png') },
        { name: 'Claude', logo: require('./assets/claude.png') },
        { name: 'DeepSeek', logo: require('./assets/deep.png') },
        { name: 'HuggingChat', logo: require('./assets/HuggingChatIcon.webp') },
        { name: 'Meta Llama', logo: require('./assets/metalama.png') },
        { name: 'eleutherai', logo: require('./assets/eai-logo.png') },
        { name: 'IBM Watson', logo: require('./assets/ibm.png') },

      ],
      additionalOptions: ['Falcon 180B', 'Mistral AI', 'OPT-175B', 'BLOOM', 'Palm 2', 'XGen-7B '],
      activePanels: [], // Tracks active panels by index
      selectedPill: null, // Tracks the selected pill in the popup
      importText: '',

       fullText:'"Train Your Prompts" is a powerful tool designed to help users refine and compare different prompts for AI systems or data-driven applications. The app allows users to dynamically add panels, input API endpoints, and compare results side-by-side. With features like file imports and interactive comparisons, it provides a seamless environment to experiment, test, and optimize prompts for maximum efficiency. Whether you\'re exploring AI tools or evaluating APIs, this app makes the process intuitive and efficient. To begin, simply select one of the option pills at the top of the interface to assign an API to a comparison panel. When the option pill is selected, a dialog appears where you can input the API Name (to label it in the UI) and the API Endpoint URL. An example of an API endpoint might look like this: https://api.example.com/generate-response. Additionally, you can provide optional parameters like an API key for authentication or query parameters for specific behaviors. Once you click the “Add” button, the API becomes associated with the respective panel, and you’re ready to compare its output. or instance, let’s say you’re comparing three APIs: OpenAI’s ChatGPT, Microsoft’s Azure AI, and Google’s Bard. You can input their respective endpoints and send a sample text like, “Explain the concept of machine learning.” Each panel will display the corresponding API’s response, allowing you to evaluate differences in content quality, tone, or accuracy. This side-by-side comparison empowers you to identify the API that best meets your requirements. If you’re unsure how to find API endpoints, most APIs have comprehensive documentation available on their provider’s website.',


      displayedText: '', // Dynamically rendered text

    };
  },
   mounted() {
    // Call typeText when the component is mounted
    this.typeText();
  },

  methods: {
    openDialog(index) {
      this.selectedPill = index; // Pre-select the pill
      this.dialog = true; // Open the popup
    },
    selectPill(index) {
      this.selectedPill = index; // Update selected pill
    },
    addPanel() {
      if (this.selectedPill !== null && !this.activePanels.includes(this.selectedPill)) {
        this.activePanels.push(this.selectedPill); // Add the panel
        this.dialog = false; // Close the popup
      }
    },
    removePanel(index) {
      const panelIndex = this.activePanels.indexOf(index);
      if (panelIndex > -1) {
        this.activePanels.splice(panelIndex, 1); // Remove the panel
      }
    },
    handleDropdown(option) {
      alert(`Selected: ${option}`);
    },
    openFileBrowser() {
      alert('File browser opened!');
    },
    compareText() {
      alert('Comparing text...It is the Demo edition. Visit again soon the Beta version');
    },
     typeText() {
      let index = 0;
      const interval = setInterval(() => {
        if (index < this.fullText.length) {
          this.displayedText += this.fullText[index];
          index++;
        } else {
          clearInterval(interval); // Stop when all text is displayed
        }
      }, 30); // Typing speed
    },
  },
};
</script>


<style>
/* Custom Header Styling */
.custom-header {
  background-color: #e0e0e0;
  min-height: 130px;
  padding: 0 16px;
  border-bottom: 2px solid #ddd;
  display: flex;
  align-items: center;
}
.main-logo {
  margin-top: -30px;
  align: center;
}
.add-apis-btn {
 
margin-left:5px;
 top: 10px;
  
}

/* Option Pills Styling */

.pill-button {
  border-radius:30px;
  text-transform: none;
  width: 130px; /* Adjust for content */
  height: 60px;
  padding: 0 18px;
  display: inline;
  align-items: center;
  justify-content: center;
  margin-top:15px;
  
}

.pill-content {
  display: block;
  align-items: center;
  justify-content: center;
  width: 100%;
}

/* Text inside Pills */
.pill-text {
  font-size: 12px;
  white-space: nowrap; /* Prevents wrapping */
  text-align: center;
  margin-left: 0px; /* Space between text and logo */
}

/* Selected Pills */
.pill-selected {
  background-color:rgb(168, 212, 255);
  color: white;
  border:none;
}

/* Responsive adjustment for pills if needed */
@media screen and (max-width: 600px) {
  .pill-button {
    width: auto;
    font-size: 12px;
  }
}


/* Main Content Styling */
.main-content {
  padding: 24px;
  margin-top: 150px;
  background-color: #f9f9f9;
  min-height: calc(100vh - 100px);
}

/* Panels Styling */
.square-panel {
  height: 300px; /* Increased height */
  position: relative;
}

/* Panel Content Styling */
.panel-content {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 16px;
  height: 100%;
  background-color:rgb(247, 247, 247);
}

/* Remove Button Styling */
.remove-btn {
  position: absolute;
  top:12px;
  right: 8px;
  background-color: #ff5252;
  z-index: 1;
}

/* Import Panel Styling */
.import-panel {
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  border: 1px solid #ddd;
}

/* Import Row Styling */
.import-row {
  align-items: center;
  padding:30px;
  margin-bottom:-30px;
}

.poppils{
  margin-left: 10px;
}

/* Compare Button */
.compare-btn {
  margin-left: 12px;
  font-weight: bold;
  margin-top:-25px;
}
.app-description {
  margin-top: 20px;
}

.description-textarea {
  font-family: "Roboto", sans-serif;
  font-size: 16px;
  line-height: 1.5;
}
</style>

