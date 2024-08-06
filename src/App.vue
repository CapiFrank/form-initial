<template>
  <div ref="element" :class="print ? 'w-[8.27in] h-[11.69in] bg-green-200' : 'w-screen min-h-screen'">
    <div class="w-full flex justify-center items-center font-noto" :class="{ 'p-20': print }">
      <div class="bg-[rgba(255,255,255,0.5)] rounded-md shadow-[0_4px_30px_rgba(0,0,0,0.1)] border-[1px_solid_rgba(255,255,255,0.3)]">
        <form class="flex flex-col gap-2 py-2 px-8" @submit.prevent="submit">
          <span class="self-center text-xl font-bold uppercase py-2">Datos Del Cliente</span>
          <InputText text="Persona de contacto:" placeholder="Nombre completo" name="contacto" v-model="properties.contacto" />
          <InputText text="Nombre del comercio:" placeholder="Nombre de fantasía" name="fantasia" v-model="properties.fantasia" />
          <InputText text="Razón social:" placeholder="Nombre físico o jurídico" name="razonsocial" v-model="properties.razonsocial" />
          <InputNumber text="Número de cédula física o jurídica:" placeholder="101230321" name="cedula" v-model="properties.cedula" />
          <RadioButton text="Régimen del negocio:" :options="['Simplificado', 'Tradicional']" name="regimen" v-model="properties.regimen" />
          <InputDatalist text="Tipo de negocio" :options="['Soda', 'Restaurante', 'Cafeteria', 'Supermercado', 'Salon de belleza', 'Tienda de ropa']" name="tiponegocio" v-model="properties.tiponegocio"></InputDatalist>
          <RadioButton text="Posee internet en el local?:" :options="['Si', 'No']" name="internet" v-model="properties.internet" />
          <InputCheckbox text="Métodos de pago a usar:" :options="['Efectivo', 'Dolares', 'Tarjeta', 'Sinpe Movil', 'Credito', 'Pinpad']" name="metodopago" v-model="properties.metodopago"></InputCheckbox>
          <InputCheckbox text="Para qué desea el sistema?:" :options="['Vender', 'Llevar inventarios']" name="sistema" v-model="properties.sistema"></InputCheckbox>
          <InputCheckbox v-show="properties.sistema.includes('Llevar inventarios')" text="Cómo desea llevar el inventario?:" :options="['Por unidades', 'Por gramaje(Recetas)']" name="inventario" v-model="properties.inventario"></InputCheckbox>
          <InputCheckbox text="Cuáles de los siguientes aspectos son más importantes para usted?:" name="expectativas" :options="['Mejorar el tiempo de cobro', 'Optimizar el control de estadísticas y ventas', 'Fortalecer el control de empleados', 'Mejorar la integración con la tecnología']" v-model="properties.expectativas"></InputCheckbox>
          <button type="submit" class="bg-green-400 p-2 m-2 w-28 rounded-md self-center">Descargar</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, nextTick } from "vue";
import InputText from "@/components/InputText.vue";
import InputNumber from "@/components/InputNumber.vue";
import RadioButton from "@/components/RadioButton.vue";
import InputDatalist from "@/components/InputDatalist.vue";
import InputCheckbox from "@/components/InputCheckbox.vue";
import domtoimage from 'dom-to-image';
import { jsPDF } from "jspdf";
const element = ref(null);
const print = ref(false);
const properties = ref({
  contacto: '',
  fantasia: '',
  razonsocial: '',
  cedula: '',
  regimen: '',
  tiponegocio: '',
  internet: '',
  metodopago: [],
  sistema: [],
  inventario: [],
  expectativas: []
})
const exportFilename = ref(`datos_adicionales.pdf`);
const submit = async () => {
  print.value = !print.value;
  await nextTick();
  await convertToPDF();
  print.value = !print.value;
}
const convertToPDF = () => {
  return new Promise(async (resolve, reject) => {
    try {
      // Captura el contenido de la página web como una imagen
      var scale = 2;
      const canvas = await domtoimage.toPng(element.value, {
        quality: 1.0,
        width: element.value.clientWidth * scale,
        height: element.value.clientHeight * scale,
        style: {
          transform: 'scale(' + scale + ')',
          transformOrigin: 'top left'
        }
      });

      // Crea un nuevo objeto jsPDF con las dimensiones ajustadas
      const pdf = new jsPDF({
        orientation: 'portrait',
        unit: 'pt',
        format: 'letter',
      });

      // Agrega la imagen capturada al PDF
      pdf.addImage(canvas, 'PNG', 0, 0, pdf.internal.pageSize.width, pdf.internal.pageSize.height, '', 'FAST');

      // Guarda o muestra el PDF
      pdf.save(exportFilename.value);

      // Resuelve la promesa después de completar la operación
      resolve();
    } catch (error) {
      // Rechaza la promesa en caso de error
      reject(error);
    }
  });
};
</script>
