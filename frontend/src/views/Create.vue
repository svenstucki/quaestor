<template>
  <b-container
    fluid
    id="content"
    style="padding-left: 10em; padding-right: 10em"
  >
    <b-row>
      <b-col>
        <b-button class="mb-5" size="sm" variant="outline-success" @click="save"
          >Save</b-button
        >
        <b-button
          class="mb-5 ml-3"
          size="sm"
          variant="outline-success"
          @click="download"
          >Download</b-button
        >
      </b-col>
    </b-row>
    <b-row>
      <b-col>
        <b-form>
          <b-form-group description="Language" label-for="language">
            <b-form-select
              id="language"
              v-model="invoice.language"
              class="mb-3"
            >
              <b-form-select-option value="en-US">English</b-form-select-option>
              <b-form-select-option value="de-DE">Deutsch</b-form-select-option>
            </b-form-select>
          </b-form-group>

          <b-form-group
            description="Invoice Date"
            label-for="invoice-datepicker"
          >
            <b-form-datepicker
              id="invoice-datepicker"
              v-model="invoice.date"
              class="mb-2"
            ></b-form-datepicker>
          </b-form-group>

          <b-form-group description="Due date" label-for="due-datepicker">
            <b-form-datepicker
              id="due-datepicker"
              v-model="invoice.due"
              class="mb-2"
            ></b-form-datepicker>
          </b-form-group>

          <b-form-group
            description="Invoice Number"
            label-for="invoice-no"
            invalid-feedback="Please specify an invoice number."
          >
            <b-form-input
              id="invoice-no"
              v-model="invoice.no"
              trim
            ></b-form-input>
          </b-form-group>

          <b-form-group
            description="Customer Address"
            label-for="customer-address"
            invalid-feedback="Please specify an invoice number."
            :state="invoice.address.length > 0"
          >
            <b-form-textarea
              id="customer-address"
              v-model="invoice.address"
              :state="invoice.address.length > 0"
              trim
              style="height: 7em"
            ></b-form-textarea>
          </b-form-group>

          <b-form-group
            description="Contact Person"
            label-for="contact-person"
            invalid-feedback="Please specify a contact person."
          >
            <b-form-input
              id="Contact Person"
              v-model="invoice.contact"
              trim
            ></b-form-input>
          </b-form-group>

          <b-form-group
            description="Customer Reference"
            label-for="customer-reference"
          >
            <b-form-input
              id="customer-reference"
              v-model="invoice.reference"
              trim
            ></b-form-input>
          </b-form-group>

          <b-form-group
            description="Title"
            label-for="title"
          >
            <b-form-input
              id="title"
              v-model="invoice.title"
              trim
            ></b-form-input>
          </b-form-group>
          <b-form-group
            description="Invoice text"
            label-for="text"
            invalid-feedback="Please specify the invoice text."
            :state="invoice.text.length > 0"
          >
            <b-form-textarea
              id="text"
              v-model="invoice.text"
              :state="invoice.text.length > 0"
              trim
              style="height: 15em"
            ></b-form-textarea>
          </b-form-group>
          <b-form-group
            description="Additional text"
            label-for="additional-text"
          >
            <b-form-textarea
              id="additional-text"
              v-model="invoice.additonal_text"
              trim
              style="height: 15em"
            ></b-form-textarea>
          </b-form-group>
          <b-form-group
            description="Currency"
            invalid-feedback="Please specify a currency."
            :state="invoice.currency.length > 0"
          >
            <b-form-input
              v-model="invoice.currency"
              :state="invoice.currency.length > 0"
              trim
            ></b-form-input>
          </b-form-group>

          <b-form-group description="VAT" label-for="add-vat">
            <b-input-group>
              <b-form-input
                id="add-vat"
                type="number"
                number
                v-model="invoice.vat_rate"
                trim
              ></b-form-input>
              <b-input-group-append>
                <b-input-group-text class="bg-transparent font-weight-bold">
                  %
                </b-input-group-text>
              </b-input-group-append>
            </b-input-group>
          </b-form-group>

          <b-container
            class="p-0 pt-4 pl-3 pr-3 mb-4 border border-secondary rounded"
          >
            <draggable
              v-model="invoice.positions"
              group="people"
              @start="drag = true"
              @end="drag = false"
            >
              <div v-for="(position, idx) in invoice.positions" :key="position.id">
                <hr v-if="idx != 0" />
                <b-row>
                  <b-col>
                    <b-form-group
                      description="Date"
                      invalid-feedback="Please provide a date for the position."
                      :state="position.date.length > 0"
                    >
                      <b-form-input
                        v-model="position.date"
                        :state="position.date.length > 0"
                        trim
                      ></b-form-input>
                    </b-form-group>
                  </b-col>
                  <b-col>
                    <b-form-group
                      description="Description"
                      invalid-feedback="Please specify a position description."
                      :state="position.text.length > 0"
                    >
                      <b-form-input
                        v-model="position.text"
                        :state="position.text.length > 0"
                        trim
                      ></b-form-input>
                    </b-form-group>
                  </b-col>
                </b-row>
                <b-row>
                  <b-col>
                    <b-form-group
                      description="Count"
                      invalid-feedback="Please select a count > 0."
                      :state="position.count > 0"
                    >
                      <b-form-input
                        v-model="position.count"
                        :state="position.count > 0"
                        trim
                        type="number"
                        number
                      ></b-form-input>
                    </b-form-group>
                  </b-col>
                  <b-col>
                    <b-form-group
                      description="Cost per piece"
                      invalid-feedback="Please select a cost > 0."
                      :state="position.cost > 0"
                    >
                      <b-form-input
                        v-model="position.cost"
                        :state="position.cost > 0"
                        trim
                        type="number"
                        number
                      ></b-form-input>
                    </b-form-group>
                    <h2 style="float: right">
                      <b-icon icon="grip-vertical"></b-icon>
                    </h2>
                    <h2 style="float: right">
                      <b-icon icon="trash" @click="removePosition(position.id)"></b-icon>
                    </h2>
                  </b-col>
                </b-row>
              </div>
            </draggable>
          </b-container>
          <b-button
            class="mb-5"
            size="sm"
            variant="outline-primary"
            @click="addPosition"
            >Add Position</b-button
          >

          <b-form-group
            description="Accent color"
            invalid-feedback="Please specify the company accent color."
          >
            <b-form-input
              v-model="invoice.company.accent_color"
              :state="invoice.company.accent_color.length > 0"
              trim
              type="color"
            ></b-form-input>
          </b-form-group>
          <b-form-group
            description="Text color"
            invalid-feedback="Please specify the company text color."
          >
            <b-form-input
              v-model="invoice.company.text_color"
              :state="invoice.company.text_color.length > 0"
              trim
              type="color"
            ></b-form-input>
          </b-form-group>
          <b-form-group
            description="Text color muted"
            invalid-feedback="Please specify the company muted text color."
          >
            <b-form-input
              v-model="invoice.company.text_color_muted"
              :state="invoice.company.text_color_muted.length > 0"
              trim
              type="color"
            ></b-form-input>
          </b-form-group>
        </b-form>
      </b-col>

      <b-col class="col-100">
        <div id="pdf">
          <pdf :src="preview"></pdf>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>

<script lang="ts">
import pdf from 'vue-pdf';
import { Component, Vue } from 'vue-property-decorator';
import axios from 'axios';
import draggable from 'vuedraggable';
import { API_URL } from '@/globals';

@Component({
  components: {
    pdf,
    draggable,
  },
  data() {
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());

    setTimeout(() => {
      axios
        .post(`${API_URL}/generate`, this.$data.invoice, {
          responseType: 'arraybuffer',
        })
        .then((response) => {
          const data = new Uint8Array(response.data);
          this.$data.preview = pdf.createLoadingTask(data);
        });
    }, 1000);

    const due = new Date();
    due.setDate(today.getDate() + 30);

    return {
      preview: undefined,
      position_count: 1,
      invoice: {
        language: 'de-DE',
        company: {
          accent_color: '#2176AE',
          text_color: '#232020',
          text_color_muted: '#524D4D',
        },
        date: today.toISOString().split('T')[0],
        due: due.toISOString().split('T')[0],
        title: 'TATA',
        address: 'XYZ AG\nPostfach 1234\n8001 Zürich',
        no: 'RE19-24',
        contact: 'John Doe',
        reference: '123123123',
        text:
          'Sehr geehrte Damen und Herren\n\nVielen Dank für das entgegengebrachte Vertrauen und die Beauftragung mit der Softwareentwicklung. Gemäss Offerte XY erlauben wir uns, Ihnen die untenstehenden Leistungen in Rechnung zu stellen.\n\nBest Grüsse  \nJohn Doe',
        additonal_text: '',
        positions: [
          {
            id: 0,
            date: '1.1.2024',
            text: 'Test',
            count: 42,
            cost: 120,
            vat_included: false,
            vat_must: true,
          },
        ],
        currency: 'CHF',
        vat_rate: 8.1,
      },
    };
  },
  watch: {
    invoice: {
      handler(_newValue) {
        axios
          .post(`${API_URL}/generate`, this.$data.invoice, {
            responseType: 'arraybuffer',
          })
          .then((response) => {
            const data = new Uint8Array(response.data);
            console.log(data);
            this.$data.preview = pdf.createLoadingTask(data);
          });
      },
      deep: true,
    },
  },
  methods: {
    addPosition() {
      this.$data.invoice.positions.push({
        id: this.$data.position_count,
        date: '1.1.2024',
        text: 'Test',
        count: 42,
        cost: 120,
        vat_included: false,
        vat_must: true,
      });
      this.$data.position_count += 1;
    },
    removePosition(positionId) {
      if (this.$data.invoice.positions.length <= 1) {
        return;
      }
      const idx = this.$data.invoice.positions.findIndex((el: any) => el.id === positionId);
      if (idx < 0) {
        console.error('should not happen');
        return;
      }
      this.$delete(this.$data.invoice.positions, idx);
    },
    save() {
      return axios
        .post(`${API_URL}/store`, this.$data.invoice, {
          responseType: 'arraybuffer',
        })
        .then((response) => {
          this.$bvToast.toast('Successfully saved invoice.', {
            title: 'Success!',
            variant: 'success',
            autoHideDelay: 5000,
            appendToast: true,
          });
        })
        .catch((response) => {
          this.$bvToast.toast('Failed to save invoice.', {
            title: 'Failure!',
            variant: 'danger',
            autoHideDelay: 5000,
            appendToast: true,
          });
        });
    },
    download() {
      (this as any).save().then((_: any) => {
        axios
          .post(`${API_URL}/generate`, this.$data.invoice, {
            responseType: 'blob',
          })
          .then((response: any) => {
            const url = window.URL.createObjectURL(new Blob([response.data]));
            const link = document.createElement('a');
            link.href = url;
            link.setAttribute('download', 'invoice.pdf');
            document.body.appendChild(link);
            link.click();
          });
      });
    },
  },
})
export default class Home extends Vue {}
</script>

<style socped lang="scss">
#content {
  min-height: calc(100% - 84px);
}

.row {
  min-height: 100%;
}

.col-100 {
  height: 100%;
}

div#pdf {
  border: 1px solid black;
  max-height: 100%;
  overflow: scroll;
}
</style>
