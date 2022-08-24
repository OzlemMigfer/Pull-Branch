<template>
    <!-- <h1>PullBranch</h1> -->

    <div id="app">
        <!-- cascading select dropdown -->
        <div class="cascading-dropdown">
            <div class="dropdown">
                <span>Lisans Türü : </span>
                <select @change="onChange" id="licence" v-model="selectedLicence" :required="true">
                    <option value="">Lisans Türü Seçiniz</option>
                    <option v-for="(item, index) in items" :key="item.id" :value="index">{{index}}{{ item.name }}</option>
                </select>
            </div>
            <div class="dropdown">
                <span>Sınıf Seviyesi : </span>
                <select @change="onChange" id="grade" v-model="selectedGrade" :disabled="indexes.length == 0">
                    <option value="">Sınıf Seviyesi Seçiniz</option>
                    <option v-for="(index) in indexes" :key="index.id">{{ index }}{{ index.id }}</option>
                </select>
            </div>
            <!-- toggle-switch for 'genel kullanım' -->
            <div class="mr-n16">
              <v-container
                class="px-0 d-flex justify-end mr-n16 mt-n16 "
                fluid
              >
                <v-switch
                  v-model="switch1"
                  :label="` Genel Kullanım`"
                ></v-switch>
              </v-container>
            </div>
        </div>

        <!-- excel dosyası yuklemek -->
        <div class="uploadExcel">
          <input 
              type="file" 
              accept=".xls,.xlsx"
              class="my_data mt-8 mb-5"
              @change="importExcel"
              id="upload" 
          />
          <v-spacer></v-spacer>
          <v-data-table
            id="selectBranch"
            v-model="selected"
            item-key="sıra"
            show-select
            :headers="headers"
            :items="branches"
            sort-by="sıra"
          >       
          </v-data-table>
          <v-row class="d-flex justify-end mr-5 mt-5 mb-5">
            <v-btn
              depressed
              color="primary"
              id="saveBtn"
              @click="save"
            >
            Kaydet
            </v-btn>
          </v-row>         
        </div>        
    </div>
</template>





<script>
// import xlsx  from "xlsx";
import * as xlsx from 'xlsx';

export default{
    data: () => ({
        items: {
          "İlkokul": [1, 2, 3, 4, 5],
          "Ortaokul": [6, 7, 8],
          "Lise": [9, 10, 11, 12]
        },
        indexes: [],
        selectedLicence: "",
        selectedGrade: "",

        //excel
        selected: [],
        headers: [
          {text: "Sıra", value: "sıra"},
          {text: "Şube", value: "sube"}
        ],
        branches: [],
        switch1: true      
    }),
    watch: {
        selectedLicence: function () {
            // Önceki değeri temizler
            this.indexes = [];
            this.selectedGrade = "";
            // Seçilen lisansa göre sınıfları getir
            if (this.selectedLicence.length > 0) {
                this.indexes = this.items[this.selectedLicence];
            }
        }
    },
    methods: {
        // excel dosyası yuklemek
        importExcel(e) {
          const files = e.target.files;
          if (!files.length) {
            return ;
          } else if (!/\.(xls|xlsx)$/.test(files[0].name.toLowerCase())) {
            return alert("Yükleme biçimi yanlış. Lütfen xls veya xlsx formatında dosya yükleyin.");
          }
          const fileReader = new FileReader();
          fileReader.onload = ev => {
            try {      
              const data = ev.target.result;
              const XLSX = xlsx;
              const workbook = XLSX.read(data, {
                type: 'binary'
              });           
              const wsname = workbook.SheetNames[0]; 
              const importedBranch = XLSX.utils.sheet_to_json(workbook.Sheets[wsname]);
              // document.getElementById("jsondata").innerHTML = JSON.stringify(importedBranch);
              this.branches = this.branches.concat(importedBranch);
              this.addAutoSortNumbers();
            } catch (e) {
              return alert("Read failure!"+e);
            }
          };
          fileReader.readAsBinaryString(files[0]);
          var input = document.getElementById("upload");
          input.value = "";
        },
        addAutoSortNumbers(){
          let number = 1;
          this.branches.forEach( branch => {
            branch.sıra = number;
            number++;
          });
        },
        //kaydet butonu
        save(){
          //seçilen dataları kontrol etmke için
          document.getElementById("licence").innerHTML = JSON.stringify(this.selectedLicence);
          document.getElementById("grade").innerHTML = JSON.stringify(this.selectedGrade);
          document.getElementById("selectBranch").innerHTML = JSON.stringify(this.selected);
        },
        onChange() {         
          console.log(this.selected,this.selectedLicence,this.selectedGrade);
        }
        
    }
};
</script>

<style>

.cascading-dropdown{
    margin: auto;
    margin-top: 50px;
    width: 50%;
}
.dropdown {
    display: inline-flex;
    padding: 10px 5px;
    /* border-bottom: 1px solid #DDD; */
}

.dropdown span {
    padding-left: 50px;
    width: 160px;
}
</style>