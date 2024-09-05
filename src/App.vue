<template>
  <div :class="!print ? 'bg-green-200' : ''">
    <div ref="element" :class="print ? 'w-[8.27in] h-[11.69in]' : 'w-screen min-h-screen'">
      <div class="w-full flex justify-center items-center font-noto" :class="{ 'p-20': print }">
        <div
          class="bg-[rgba(255,255,255,0.5)] rounded-md shadow-[0_4px_30px_rgba(0,0,0,0.1)] border-[1px_solid_rgba(255,255,255,0.3)]">
          <form class="flex flex-col gap-2 py-2 px-8" @submit.prevent="submit">
            <span class="self-center text-xl font-bold uppercase py-2 text-nowrap">Datos Del Cliente</span>
            <InputText required text="Persona de contacto" placeholder="Nombre completo" name="contacto"
              v-model="properties.contacto" />
            <InputText required text="Nombre del comercio" placeholder="Nombre de fantasía" name="fantasia"
              v-model="properties.fantasia" />
            <InputText required text="Razón social" placeholder="Nombre físico o jurídico" name="razonsocial"
              v-model="properties.razonsocial" />
            <InputNumber required text="Número de cédula física o jurídica" placeholder="101230321" name="cedula"
              v-model="properties.cedula" />
            <RadioButton required text="Régimen del negocio" placeholder="Restaurante"
              :options="['Simplificado', 'Tradicional']" name="regimen" v-model="properties.regimen" />
            <InputDatalist required text="Tipo de negocio"
              :options="['Soda', 'Restaurante', 'Cafeteria', 'Supermercado', 'Salon de belleza', 'Tienda de ropa']"
              name="tiponegocio" v-model="properties.tiponegocio"></InputDatalist>
            <RadioButton required text="Posee internet en el local?" :options="['Si', 'No']" name="internet"
              v-model="properties.internet" />
            <InputCheckbox required text="Métodos de pago a usar"
              :options="['Efectivo', 'Dolares', 'Tarjeta', 'Sinpe Movil', 'Credito', 'Pinpad']" name="metodopago"
              v-model="properties.metodopago"></InputCheckbox>
            <InputCheckbox required text="Para qué desea el sistema?" :options="['Vender', 'Llevar inventarios']"
              name="sistema" v-model="properties.sistema"></InputCheckbox>
            <InputCheckbox required v-if="properties.sistema.includes('Llevar inventarios')"
              text="Cómo desea llevar el inventario?:" :options="['Por unidades', 'Por gramaje(Recetas)']"
              name="inventario" v-model="properties.inventario"></InputCheckbox>
            <InputCheckbox required text="Cuáles de los siguientes aspectos son más importantes para usted?"
              name="expectativas"
              :options="['Mejorar el tiempo de cobro', 'Optimizar el control de estadísticas y ventas', 'Fortalecer el control de empleados', 'Mejorar la integración con la tecnología']"
              v-model="properties.expectativas"></InputCheckbox>
            <div v-if="!print" class="flex self-center">
              <button type="submit" @click="() => { isDownload = true; }"
                class="bg-green-400 font-bold text-white border-green-500 hover:bg-green-500 focus:bg-green-600 border-2 p-2 m-2 w-28 rounded-md self-center">Descargar</button>
              <button type="submit" @click="() => { isDownload = false; }"
                class="bg-blue-400 font-bold text-white border-blue-500 hover:bg-blue-500 focus:bg-blue-600 border-2 p-2 m-2 w-28 rounded-md self-center">Compartir</button>
            </div>
          </form>
        </div>
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
const isDownload = ref(true);
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
  if (isDownload.value) {
    const pdf = await convertToPDF();
    pdf.save(exportFilename.value);
  } else {
    await sharePDF();
  }
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
      // pdf.save(exportFilename.value);

      // // Resuelve la promesa después de completar la operación
      resolve(pdf);
    } catch (error) {
      // Rechaza la promesa en caso de error
      reject(error);
    }
  });
};
const sharePDF = async () => {
  try {
    // Genera el PDF usando convertToPDF
    const pdf = await convertToPDF();

    // Solo continúa si la API Web Share está disponible
    // Verifica si el navegador soporta la API de Web Share con archivos

    const pdfBlob = new Blob([pdf.output('arraybuffer')], { type: 'application/pdf' });
    const file = new File([pdfBlob], exportFilename.value, { type: 'application/pdf' });
    if (navigator.canShare && navigator.canShare({ files: [file] })) {


      await navigator.share({
        title: 'Datos del Cliente',
        text: 'Por favor, revise el documento adjunto.',
        files: [file]
      });
    } else {
      // Proporciona una opción de descarga como alternativa
      const pdfBlob = new Blob([pdf.output('arraybuffer')], { type: 'application/pdf' });
      const pdfURL = URL.createObjectURL(pdfBlob);
      const link = document.createElement('a');
      link.href = pdfURL;
      link.download = exportFilename.value;
      link.click();

      alert("Compartir no está soportado, el PDF se ha descargado. Compártelo manualmente desde tu gestor de archivos.");
    }
  } catch (error) {
    console.error("Error al compartir el PDF:", error);
  }
};

</script>
