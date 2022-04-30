<template>
  <q-card class="my-card">
    <q-img :src="user.picture.large">
      <div class="absolute-bottom text-h6">
        {{ user.name.title }} {{ user.name.last }} {{ user.name.first }}
      </div>
    </q-img>

    <q-card-section>
      <span class="text-grey"
        >Nationality <q-badge outline color="secondary" :label="user.nat"
      /></span>
      <div class="text-h6">{{ user.location.city }}</div>
      <div class="text-subtitle2">in {{ user.location.state }}</div>
    </q-card-section>
    <div class="row justify-center">
      <q-btn-group flat>
        <q-btn @click="details = true" color="info" flat icon="search" />
        <q-btn @click="contact = true" flat color="secondary" icon="call" />
        <q-btn @click="location = true" flat color="accent" icon="pin_drop" />
      </q-btn-group>
    </div>
  </q-card>
  <q-dialog v-model="location">
    <q-card>
      <q-card-section class="row items-center q-pb-none">
        <div class="text-h6">
          <q-avatar> <img :src="user.picture.medium" /> </q-avatar
          >{{ user.name.first }} location : {{ user.location.coordinates.latitude }},
          {{ user.location.coordinates.longitude }}
        </div>
        <q-space />
        <q-btn icon="close" flat round dense v-close-popup />
      </q-card-section>

      <q-card-section>
        <p>
          <strong>Address :</strong> {{ user.location.street.number }}
          {{ user.location.street.name }}, {{ user.location.postcode }}
        </p>
        <p><strong>City :</strong>{{ user.location.city }}</p>
        <p><strong>State :</strong>{{ user.location.state }}</p>
      </q-card-section>
      <q-separator />
      <q-card-section>
        {{ user.location.timezone.description }}
      </q-card-section>
    </q-card>
  </q-dialog>
  <q-dialog v-model="contact">
    <q-card>
      <q-card-section class="row items-center q-pb-none">
        <div class="text-h6">
          <q-avatar> <img :src="user.picture.medium" /> </q-avatar> Contact
          {{ user.name.first }}
        </div>
        <q-space />
        <q-btn icon="close" flat round dense v-close-popup />
      </q-card-section>

      <q-list class="q-mt-sm">
        <q-item clickable v-ripple>
          <q-item-section avatar>
            <q-avatar icon="mail" />
          </q-item-section>

          <q-item-section>{{ user.email }}</q-item-section>
        </q-item>

        <q-item clickable v-ripple>
          <q-item-section avatar>
            <q-avatar icon="smartphone" />
          </q-item-section>

          <q-item-section>{{ user.phone }}</q-item-section>
        </q-item>
      </q-list>
    </q-card>
  </q-dialog>
  <q-dialog v-model="details">
    <q-card style="height: 80%; width: 80%" class="my-card" id="pdf">
      <q-card-section class="column items-center">
        <q-btn
          v-if="!loading"
          color="secondary"
          flat
          class="q-mt-sm q-mb-sm"
          @click="toPdf"
          label="Download the sheet"
          icon="download"
        />
        <q-avatar size="200px"><q-img :src="user.picture.large" /></q-avatar>
        <p class="text-bold q-ma-none" style="font-size: 18px">
          {{ user.name.first }} {{ user.name.last }}
        </p>
        <p class="text-grey">{{ user.dob.age }} years old</p>
      </q-card-section>
      <p class="text-h6 text-center">Contact</p>
      <q-card-section class="q-mr-md q-mb-md q-ml-md">
        <q-list>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Mobile</q-item-label>
              <q-item-label caption lines="2"> {{ user.phone }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="call" />
            </q-item-section>
          </q-item>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Mail</q-item-label>
              <q-item-label caption lines="2"> {{ user.email }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="mail" />
            </q-item-section>
          </q-item>
        </q-list>
      </q-card-section>
      <p class="text-h6 text-center">Location</p>
      <q-card-section class="q-mr-md q-mb-md q-ml-md">
        <q-list>
          <q-item clickable>
            <q-item-section>
              <q-item-label>City</q-item-label>
              <q-item-label caption lines="2"> {{ user.location.city }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="location_city" />
            </q-item-section>
          </q-item>
          <q-item clickable>
            <q-item-section>
              <q-item-label>State</q-item-label>
              <q-item-label caption lines="2"> {{ user.location.state }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="flag" />
            </q-item-section>
          </q-item>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Postal code</q-item-label>
              <q-item-label caption lines="2"> {{ user.location.postcode }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="apartment" />
            </q-item-section>
          </q-item>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Coordinates</q-item-label>
              <q-item-label caption lines="2">
                {{ user.location.coordinates.latitude }},
                {{ user.location.coordinates.longitude }}</q-item-label
              >
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="person_pin_circle" />
            </q-item-section>
          </q-item>
        </q-list>
      </q-card-section>
      <p class="text-h6 text-center">Login informations</p>
      <q-card-section class="q-mr-md q-mb-md q-ml-md">
        <q-list>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Username</q-item-label>
              <q-item-label caption lines="2"> {{ user.login.username }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="person" />
            </q-item-section>
          </q-item>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Uid</q-item-label>
              <q-item-label caption lines="2"> {{ user.login.uuid }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="badge" />
            </q-item-section>
          </q-item>
          <p class="text-center q-mt-sm text-grey text-bold">Password</p>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Password</q-item-label>
              <q-item-label caption lines="2"> {{ user.login.password }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="password" />
            </q-item-section>
          </q-item>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Md5</q-item-label>
              <q-item-label caption lines="2"> {{ user.login.md5 }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="key" />
            </q-item-section>
          </q-item>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Sha1</q-item-label>
              <q-item-label caption lines="2"> {{ user.login.sha1 }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="key" />
            </q-item-section>
          </q-item>
          <q-item clickable>
            <q-item-section>
              <q-item-label>Sha256</q-item-label>
              <q-item-label caption lines="2"> {{ user.login.sha256 }}</q-item-label>
            </q-item-section>

            <q-item-section side top>
              <q-icon color="dark" name="key" />
            </q-item-section>
          </q-item>
        </q-list> </q-card-section
    ></q-card>
    <div id="pdfFin" />
  </q-dialog>
</template>

<script>
import html2pdf from "html2pdf.js";
import { useQuasar } from "quasar";
export default {
  props: {
    user: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      loading: false,
      details: false,
      location: false,
      contact: false,
      current: this.user,
      utils: useQuasar(),
    };
  },
  methods: {
    async toPdf() {
      this.loading = true;
      this.utils.loading.show();
      var opt = {
        margin: 1,
        filename: this.current.name.first + "_" + this.current.name.last + "_sheet.pdf",
        image: { type: "jpeg", quality: 0.98 },
        html2canvas: { scale: 2 },
        jsPDF: { unit: "pt", format: "legal", orientation: "portrait" },
        pagebreak: {
          before: ".beforeClass",
          after: ["#pdf", "#pdfFin"],
        },
      };
      await html2pdf().set(opt).from(document.getElementById("pdf")).save();
      this.utils.loading.hide();
      this.loading = false;
    },
  },
};
</script>
