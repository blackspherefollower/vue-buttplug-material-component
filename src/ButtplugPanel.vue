<template>
  <v-container>
    <div v-if="!client.Connected">
      <v-btn
        color="red lighten-2"
        @click="ConnectToIntifaceDesktop"
        class="white--text ma-2"
        :disabled="isConnecting"
      >
        Connect To Intiface Desktop
      </v-btn>
      <v-btn
        color="red lighten-2"
        @click="ConnectInBrowser"
        :disabled="!HasWebBluetooth"
        class="white--text ma-2"
      >
        {{ HasWebBluetooth ? "Connect In Browser" : "Requires WebBluetooth" }}
      </v-btn>
      <v-card elevation="3" class="message-card" v-if="uiMessage">
        <v-row>
          <v-col>
            <v-btn icon @click="CloseUiMessage">
              <v-icon>close</v-icon>
            </v-btn>
          </v-col>
          <v-col class="message-flex">
            <span class="error-text" v-if="uiMessage && uiMessage[0] === 0">Error:</span>
            {{ uiMessage[1] }}
          </v-col>
        </v-row>
      </v-card>
      <v-card v-if="isConnecting">
        <v-row>
          <v-col>
            <v-progress-circular
              indeterminate
              color="purple"
            ></v-progress-circular>
            <span>Trying to connect to Intiface Desktop...</span>
          </v-col>
        </v-row>
      </v-card>
      <v-expansion-panels v-if="!isConnecting">
        <v-expansion-panel>
          <v-expansion-panel-header>
            Advanced Settings
          </v-expansion-panel-header>
          <v-expansion-panel-content>
            <v-checkbox
              v-model="scanOnConnect"
              label="Start Scanning On Connect">
            </v-checkbox>
            <v-divider />
            <v-subheader>
              Websocket Addresses
            </v-subheader>
            <v-row
              v-for="address in desktopAddresses"
              :class="address.IsValidURL ? 'address-line-correct address-line' : 'address-line-incorrect address-line'"
              :key="address.Id"
            >
              <v-col class="address-entry">
                <v-text-field
                  label="Host"
                  v-model="address.Host"
                  @change="StoreAddressCookie()"
                ></v-text-field>
              </v-col>
              <v-col class="address-entry">
                <v-text-field
                  label="Port"
                  mask="#####"
                  v-model="address.Port"
                  @change="StoreAddressCookie()"
                ></v-text-field>
              </v-col>
              <v-col>
                <v-checkbox
                  v-model="address.Insecure"
                  @change="StoreAddressCookie()"
                  label="Insecure">
                </v-checkbox>
              </v-col>
              <v-col>
                <v-checkbox
                  v-model="address.Secure"
                  @change="StoreAddressCookie()"
                  label="Secure">
                </v-checkbox>
              </v-col>
              <v-col>
                <v-btn
                  @click="RemoveAddress(address.Id)">
                  <v-icon>close</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-btn
              class="ma-2"
              @click="AddAddress()">
              <v-icon>add</v-icon>
            </v-btn>
            <v-btn
              class="ma-2"
              @click="ResetAddresses()">
              Reset
            </v-btn>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
    </div>
    <div v-if="client.Connected">
      <v-btn
        class="ma-2"
        color="red lighten-2"
        dark
        @click="Disconnect"
      >
        Disconnect
      </v-btn>
      <v-btn
        class="ma-2"
        color="red lighten-2"
        dark
        @click="ToggleScanning"
      >
        {{ isScanning ? "Stop Scanning" : "Start Scanning" }}
      </v-btn>
      <v-subheader>
        Device List
      </v-subheader>
      <div v-if="clientDevices.length === 0">No Devices Available</div>
      <v-checkbox
        v-for="device in clientDevices"
        v-model="selectedDevices"
        @change="FireChange"
        :key="device.Index"
        :value="device.Index"
        :label="device.Name"></v-checkbox>
    </div>
  </v-container>
</template>

<script lang="ts" src="./ButtplugPanel.ts">
</script>

<style lang="css">
 .address-entry {
   padding-left: 5px;
   padding-right: 5px;
 }

 @media screen and (max-width: 640px) {
   .address-line {
     flex-direction: column;
   }
 }

 .address-line-correct {
   background: #ffffff;
 }

 .address-line-incorrect {
   background: #ffeeee;
 }

 .message-card {
   padding: 10px;
   margin-top: 10px;
   margin-bottom: 10px;
 }

 .error-text {
   color: #F00;
 }

 .message-flex {
   padding: 5px;
 }
</style>
