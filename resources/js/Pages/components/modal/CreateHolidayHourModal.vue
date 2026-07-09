<template>
    <TransitionRoot as="div" :show="show">
        <Dialog as="div" class="relative z-10">
            <TransitionChild as="div" enter="ease-out duration-300" enter-from="opacity-0" enter-to="opacity-100"
                leave="ease-in duration-200" leave-from="opacity-100" leave-to="opacity-0">
                <div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" />
            </TransitionChild>
            <div class="fixed inset-0 z-10 w-screen overflow-y-auto">
                <div class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0">
                    <TransitionChild as="template" enter="ease-out duration-300"
                        enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
                        enter-to="opacity-100 translate-y-0 sm:scale-100" leave="ease-in duration-200"
                        leave-from="opacity-100 translate-y-0 sm:scale-100"
                        leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95">

                        <DialogPanel
                            class="relative transform  rounded-lg bg-white px-4 pb-4 pt-5 text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-2xl sm:p-6">

                            <Vueform ref="form$" :endpoint="submitForm" @success="handleSuccess" @error="handleError"
                                @response="handleResponse" :display-errors="false">
                                <HiddenElement name="business_hour_uuid" :meta="true" />
                                <StaticElement name="h4" tag="h4" content="Agregar nuevo feriado" />
                                <SelectElement name="holiday_type" :items="[
                                    {
                                        value: 'us_holiday',
                                        label: 'Feriado de EE. UU.',
                                    },
                                    {
                                        value: 'ca_holiday',
                                        label: 'Feriado canadiense',
                                    },
                                    {
                                        value: 'uk_holiday',
                                        label: 'Feriado del Reino Unido',
                                    },
                                    {
                                        value: 'single_date',
                                        label: 'Fecha única',
                                    },
                                    {
                                        value: 'date_range',
                                        label: 'Rango de fechas',
                                    },
                                    {
                                        value: 'recurring_pattern',
                                        label: 'Patrón recurrente',
                                    },
                                ]" :search="true" :native="false" label="Tipo de feriado" input-type="search"
                                    @change="handleHolidayTypeChange" autocomplete="off" placeholder="Seleccione el tipo de feriado"
                                    :floating="false" />


                                <StaticElement name="p1" tag="p" :conditions="[
                                    [
                                        'holiday_type',
                                        'in',
                                        [
                                            'us_holiday',
                                            'ca_holiday',
                                            'uk_holiday',
                                        ],
                                    ],
                                ]">

                                    <div class="rounded-md bg-blue-50 p-4">
                                        <div class="flex">
                                            <div class="shrink-0">
                                                <InformationCircleIcon class="size-5 text-blue-400" aria-hidden="true" />
                                            </div>

                                            <div class="ml-3">
                                                Elija de la lista de feriados. Cada selección aplica automáticamente
                                                la excepción en esa fecha exacta del feriado.

                                            </div>
                                        </div>
                                    </div>

                                </StaticElement>

                                <StaticElement name="p2" tag="p" :conditions="[
                                    [
                                        'holiday_type',
                                        'in',
                                        [
                                            'single_date',
                                        ],
                                    ],
                                ]">

                                    <div class="rounded-md bg-blue-50 p-4">
                                        <div class="flex">
                                            <div class="shrink-0">
                                                <InformationCircleIcon class="size-5 text-blue-400" aria-hidden="true" />
                                            </div>

                                            <div class="ml-3">

                                                Defina un feriado para una fecha específica del calendario.
                                                Elija su fecha y, si es necesario, ingrese una hora de inicio y/o fin.
                                                Dejar los campos de hora en blanco cubrirá todo el día de 00:00 a 23:59.
                                            </div>
                                        </div>
                                    </div>

                                </StaticElement>

                                <StaticElement name="p3" tag="p" :conditions="[
                                    [
                                        'holiday_type',
                                        'in',
                                        [
                                            'date_range',
                                        ],
                                    ],
                                ]">

                                    <div class="rounded-md bg-blue-50 p-4">
                                        <div class="flex">
                                            <div class="shrink-0">
                                                <InformationCircleIcon class="size-5 text-blue-400" aria-hidden="true" />
                                            </div>

                                            <div class="ml-3">
                                                Cree una excepción que abarque varios días.
                                                Seleccione una fecha “Desde” y una fecha “Hasta”, y opcionalmente especifique horas de inicio/fin
                                                para cada día.
                                                Si deja los campos de hora en blanco, cada día del rango tomará por defecto una
                                                excepción de día completo.
                                            </div>
                                        </div>
                                    </div>

                                </StaticElement>

                                <StaticElement name="p" tag="p" :conditions="[
                                    [
                                        'holiday_type',
                                        'in',
                                        [
                                            'recurring_pattern',
                                        ],
                                    ],
                                ]">

                                    <div class="rounded-md bg-blue-50 p-4">
                                        <div class="flex">
                                            <div class="shrink-0">
                                                <InformationCircleIcon class="size-5 text-blue-400" aria-hidden="true" />
                                            </div>

                                            <div class="ml-3">
                                                <strong>Cómo funcionan los patrones recurrentes:</strong>
                                                Este formulario le ayuda a definir períodos de tiempo específicos basados en un
                                                <strong>patrón
                                                    recurrente</strong>.
                                                Si configura varias opciones (como un mes específico y un día específico de
                                                la semana), la condición solo se cumplirá cuando <strong>todos</strong> los criterios
                                                seleccionados sean verdaderos simultáneamente.
                                                Cualquier campo que deje en blanco no restringirá la condición (por ejemplo,
                                                dejar <strong>Mes</strong> en blanco significa “todos los meses”).
                                            </div>
                                        </div>
                                    </div>

                                </StaticElement>

                                <SelectElement name="us_holiday" :search="true" :native="false" label="Feriado de EE. UU."
                                    :submit="false" :items="usHolidays" input-type="search" autocomplete="off" :object="true"
                                    @change="handleUSHolidayUpdate" placeholder="Seleccione el feriado de EE. UU." :floating="false"
                                    :conditions="[
                                        [
                                            'holiday_type',
                                            'in',
                                            [
                                                'us_holiday',
                                            ],
                                        ],
                                    ]" />

                                <SelectElement name="ca_holiday" :search="true" :native="false" label="Feriado canadiense"
                                    :submit="false" :items="caHolidays" input-type="search" autocomplete="off" :object="true"
                                    @change="handleCAHolidayUpdate" placeholder="Seleccione el feriado canadiense" :floating="false"
                                    :conditions="[
                                        [
                                            'holiday_type',
                                            'in',
                                            [
                                                'ca_holiday',
                                            ],
                                        ],
                                    ]" />

                                <SelectElement name="uk_holiday" :search="true" :native="false" label="Feriado del Reino Unido"
                                    :submit="false" :items="ukHolidays" input-type="search" autocomplete="off" :object="true"
                                    @change="handleUKHolidayUpdate" placeholder="Seleccione el feriado del Reino Unido" :floating="false"
                                    :conditions="[
                                        [
                                            'holiday_type',
                                            'in',
                                            [
                                                'uk_holiday',
                                            ],
                                        ],
                                    ]" />

                                <TextElement name="description" label="Descripción del feriado"
                                    description="Ingrese un nombre claro y descriptivo para este feriado (por ejemplo, ‘Picnic anual de la empresa’)."
                                    :conditions="[
                                        [
                                            'holiday_type',
                                            'in',
                                            [
                                                'single_date',
                                                'date_range',
                                                'recurring_pattern',
                                            ],
                                        ],
                                    ]" />

                                <DateElement name="start_date" display-format="MMMM DD, YYYY" :label="(el$) => {
                                    if (el$.form$.el$('holiday_type').value == 'single_date') {
                                        return 'Fecha'
                                    }

                                    if (el$.form$.el$('holiday_type').value == 'date_range') {
                                        return 'Fecha de inicio'
                                    }

                                }" :columns="{
    default: {
        container: 6,
    },
    sm: {
        container: 4,
    },
}" :conditions="[
    [
        'holiday_type',
        'in',
        [
            'single_date',
            'date_range',
        ],
    ],
]" />
                                <DateElement name="start_time" label="Hora de inicio" :date="false" :time="true" :hour24="false"
                                    value-format="HH:mm" :columns="{
                                        default: {
                                            container: 6,
                                        },
                                        sm: {
                                            container: 4,
                                        },
                                    }" :conditions="[
    [
        'holiday_type',
        'in',
        [
            'single_date',
            'date_range',
        ],
    ],
]" />
                                <GroupElement name="container" :conditions="[
                                    [
                                        'holiday_type',
                                        'in',
                                        [
                                            'date_range',
                                        ],
                                    ],
                                ]" />
                                <DateElement name="end_date" display-format="MMMM DD, YYYY" label="Fecha de fin" :columns="{
                                    default: {
                                        container: 6,
                                    },
                                    sm: {
                                        container: 4,
                                    },
                                }" :conditions="[
    [
        'holiday_type',
        'in',
        [
            'date_range',
        ],
    ],
]" />
                                <DateElement name="end_time" label="Hora de fin" :date="false" :time="true" :hour24="false"
                                    value-format="HH:mm" :columns="{
                                        default: {
                                            container: 6,
                                        },
                                        sm: {
                                            container: 4,
                                        },
                                    }" :conditions="[
    [
        'holiday_type',
        'in',
        [
            'single_date',
            'date_range',
        ],
    ],
]" />
                                <GroupElement name="container_1" />

                                <SelectElement name="mon" :items="monthOptions" :search="true" :native="false"
                                    input-type="search" autocomplete="off" label="Mes" :strict="false"
                                    description="Seleccione el mes del año. Déjelo en blanco para cualquier mes." :floating="false"
                                    :conditions="[
                                        [
                                            'holiday_type',
                                            'in',
                                            [
                                                'recurring_pattern',
                                            ],
                                        ],
                                    ]" />
                                <SelectElement name="mday" :items="dayOfMonthOptions" :search="true" :native="false"
                                    input-type="search" autocomplete="off" label="Día del mes"
                                    description="Seleccione el día del mes (1-31). Por ejemplo, elija '15' para el día 15 del mes. Déjelo en blanco para cualquier día."
                                    :floating="false" :conditions="[
                                        [
                                            'holiday_type',
                                            'in',
                                            [
                                                'recurring_pattern',
                                            ],
                                        ],
                                    ]" />
                                <SelectElement name="week" :items="weekOfYearOptions" :search="true" :native="false"
                                    input-type="search" autocomplete="off" label="Semana del año" :strict="false"
                                    description="Seleccione la semana del año (1-53). La semana 1 es la que contiene el 1 de enero. Déjelo en blanco para cualquier semana."
                                    :floating="false" :conditions="[
                                        [
                                            'holiday_type',
                                            'in',
                                            [
                                                'recurring_pattern',
                                            ],
                                        ],
                                    ]" />
                                <SelectElement name="mweek" :items="weekOfMonthOptions" :search="true" :native="false"
                                    input-type="search" autocomplete="off" label="Semana del mes" :strict="false"
                                    description="Seleccione la ocurrencia de un día de la semana dentro del mes. Por ejemplo, para especificar el 2do viernes del mes, seleccione '2' aquí y 'Viernes' en el campo 'Día de la semana'. 
                                    '6' significa específicamente la última ocurrencia del día de la semana elegido en el mes. Déjelo en blanco para cualquier semana del mes."
                                    :floating="false" :conditions="[
                                        [
                                            'holiday_type',
                                            'in',
                                            [
                                                'recurring_pattern',
                                            ],
                                        ],
                                    ]" />
                                <SelectElement name="wday" :items="dayOfWeekOptions" :search="true" :native="false"
                                    input-type="search" autocomplete="off" label="Día de la semana" :strict="false"
                                    description="Seleccione el día de la semana. Esto se usa a menudo junto con 'Semana del mes'. Déjelo en blanco para cualquier día de la semana."
                                    :floating="false" :conditions="[
                                        [
                                            'holiday_type',
                                            'in',
                                            [
                                                'recurring_pattern',
                                            ],
                                        ],
                                    ]" />


                                <StaticElement name="action_header" tag="p"
                                    content="Defina cómo se gestionan las llamadas entrantes durante este feriado."
                                    :conditions="[['holiday_type', '!=', null],]" />

                                <SelectElement name="action" :items="options.routing_types" label-prop="name" :search="true"
                                    :native="false" label="Elegir acción" input-type="search" autocomplete="off"
                                    placeholder="Elegir acción" :floating="false" :strict="false"
                                    :columns="{ sm: { container: 6, }, }" @change="(newValue, oldValue, el$) => {
                                        let target = el$.form$.el$('target')

                                        // only clear when this isn’t the very first time (i.e. oldValue was set)
                                        if (oldValue !== null && oldValue !== undefined) {
                                            target.clear();
                                        }

                                        // target.clear()
                                        target.updateItems()
                                    }" :conditions="[['holiday_type', '!=', null],]" />

                                <SelectElement name="target" :items="async (query, input) => {
                                    let action = input.$parent.el$.form$.el$('action');

                                    try {
                                        let response = await action.$vueform.services.axios.post(
                                            options.routes.get_routing_options,
                                            { category: action.value }
                                        );

                                        if (input.externalValue) {
                                            const opts = response.data.options;
                                            // extract the raw value (in case externalValue might be a string or object)
                                            const lookupValue = typeof input.externalValue === 'string'
                                                ? input.externalValue
                                                : input.externalValue?.value;

                                            const selectedOption = opts.find(o => o.value === lookupValue);

                                            // console.log(selectedOption);
                                            input.update(selectedOption)
                                        }
                                        // console.log(response.data.options);
                                        return response.data.options;
                                    } catch (error) {
                                        // emits('error', error);
                                        return [];  // Return an empty array in case of error
                                    }
                                }" :search="true" label-prop="name" :native="false" label="Destino" input-type="search"
                                    allow-absent :object="true" autocomplete="off" placeholder="Elegir destino"
                                    :floating="false" :strict="false" :columns="{ sm: { container: 6, }, }" :conditions="[
                                        ['action', 'not_empty'],
                                        ['action', 'not_in', ['check_voicemail', 'company_directory', 'hangup']]
                                    ]" />


                                <GroupElement name="container_3" />
                                <ButtonElement name="reset" button-label="Cancelar" :secondary="true" :resets="true"
                                    @click="emits('close')" :columns="{
                                        container: 6,
                                    }" />
                                <ButtonElement name="submit" button-label="Guardar" :submits="true" align="right" :columns="{
                                    container: 6,
                                }" />
                            </Vueform>
                        </DialogPanel>


                    </TransitionChild>
                </div>
            </div>
        </Dialog>
    </TransitionRoot>
</template>

<script setup>
import { ref, watch } from "vue";
import { Dialog, DialogPanel, DialogTitle, TransitionChild, TransitionRoot } from '@headlessui/vue'
import { InformationCircleIcon } from '@heroicons/vue/20/solid'

const emits = defineEmits(['close', 'confirm', 'success', 'error', 'refresh-data'])

const props = defineProps({
    show: Boolean,
    options: Object,
    business_hour_uuid: String,
});

const form$ = ref(null)

const submitForm = async (FormData, form$) => {
    // Using form$.requestData will EXCLUDE conditional elements and it 
    // will submit the form as Content-Type: application/json . 
    const requestData = form$.data

    delete requestData.us_holiday;
    delete requestData.ca_holiday;
    delete requestData.uk_holiday;

    requestData.business_hour_uuid = props.business_hour_uuid

    // console.log(requestData);
    return await form$.$vueform.services.axios.post(props.options.routes.store_route, requestData)
};

function clearErrorsRecursive(el$) {
    // clear this element’s errors
    el$.messageBag?.clear()

    // if it has child elements, recurse into each
    if (el$.children$) {
        Object.values(el$.children$).forEach(childEl$ => {
            clearErrorsRecursive(childEl$)
        })
    }
}

const handleResponse = (response, form$) => {
    // Clear form including nested elements 
    Object.values(form$.elements$).forEach(el$ => {
        clearErrorsRecursive(el$)
    })

    // Display custom errors for elements
    if (response.data.errors) {
        Object.keys(response.data.errors).forEach((elName) => {
            if (form$.el$(elName)) {
                form$.el$(elName).messageBag.append(response.data.errors[elName][0])
            }
        })
    }
}

const handleSuccess = (response, form$) => {
    // console.log(response) // axios response
    // console.log(response.status) // HTTP status code
    // console.log(response.data) // response data

    emits('success', response.data.messages);
    emits('close');
    emits('refresh-data');
}

const handleError = (error, details, form$) => {
    form$.messageBag.clear() // clear message bag

    switch (details.type) {
        // Error occured while preparing elements (no submit happened)
        case 'prepare':
            console.log(error) // Error object

            form$.messageBag.append('No se pudo preparar el formulario')
            break

        // Error occured because response status is outside of 2xx
        case 'submit':
            emits('error', error);
            console.log(error) // AxiosError object
            // console.log(error.response) // axios response
            // console.log(error.response.status) // HTTP status code
            // console.log(error.response.data) // response data

            // console.log(error.response.data.errors)


            break

        // Request cancelled (no response object)
        case 'cancel':
            console.log(error) // Error object

            form$.messageBag.append('Solicitud cancelada')
            break

        // Some other errors happened (no response object)
        case 'other':
            console.log(error) // Error object

            form$.messageBag.append('No se pudo enviar el formulario')
            break
    }
}

const handleHolidayTypeChange = (newValue, oldValue, el$) => {

    if (newValue != oldValue) {
        el$.form$.clear()
        el$.form$.update({
            holiday_type: newValue
        })
    }

}

const handleUSHolidayUpdate = (newValue, oldValue, el$) => {

    if (newValue != oldValue) {

        // find the holiday whose value matches newValue
        const match = usHolidays.find(h =>
            h.value.mon === newValue.value.mon
            && h.value.mday === newValue.value.mday
            && h.value.mweek === newValue.value.mweek
            && h.value.wday === newValue.value.wday
        );

        // pull its label (or fall back to an empty string)
        const label = match?.label ?? '';

        el$.form$.update({
            mday: newValue.value.mday,
            mon: newValue.value.mon,
            mweek: newValue.value.mweek,
            wday: newValue.value.wday,
            description: label,
        })
    }

}

const handleCAHolidayUpdate = (newValue, oldValue, el$) => {
    if (newValue != oldValue) {
        // find the holiday whose value matches newValue
        const match = caHolidays.find(h =>
            h.value.mon === newValue.value.mon
            && h.value.mday === newValue.value.mday
            && h.value.mweek === newValue.value.mweek
            && h.value.wday === newValue.value.wday
        );

        // pull its label (or fall back to an empty string)
        const label = match?.label ?? '';

        el$.form$.update({
            mday: newValue.value.mday,
            mon: newValue.value.mon,
            mweek: newValue.value.mweek,
            wday: newValue.value.wday,
            description: label,
        })
    }
}

const handleUKHolidayUpdate = (newValue, oldValue, el$) => {
    if (newValue != oldValue) {
        // find the holiday whose value matches newValue
        const match = ukHolidays.find(h =>
            h.value.mon === newValue.value.mon
            && h.value.mday === newValue.value.mday
            && h.value.mweek === newValue.value.mweek
            && h.value.wday === newValue.value.wday
        );

        // pull its label (or fall back to an empty string)
        const label = match?.label ?? '';

        el$.form$.update({
            mday: newValue.value.mday,
            mon: newValue.value.mon,
            mweek: newValue.value.mweek,
            wday: newValue.value.wday,
            description: label,
        })
    }
}

// Month (1=Jan … 12=Dec)
const monthOptions = [
  { value: '1',  label: 'Enero' },
  { value: '2',  label: 'Febrero' },
  { value: '3',  label: 'Marzo' },
  { value: '4',  label: 'Abril' },
  { value: '5',  label: 'Mayo' },
  { value: '6',  label: 'Junio' },
  { value: '7',  label: 'Julio' },
  { value: '8',  label: 'Agosto' },
  { value: '9',  label: 'Septiembre' },
  { value: '10', label: 'Octubre' },
  { value: '11', label: 'Noviembre' },
  { value: '12', label: 'Diciembre' },
];

// Day of Month (1–31)
const dayOfMonthOptions = Array.from({ length: 31 }, (_, i) => ({
  value: String(i + 1),
  label: String(i + 1),
}));

// Week of Year (1–53)
const weekOfYearOptions = Array.from({ length: 53 }, (_, i) => ({
  value: String(i + 1),
  label: String(i + 1),
}));

// Week of Month (1=first … 5=fifth, 6=last)
const weekOfMonthOptions = [
  { value: '1', label: '1 (Primera)' },
  { value: '2', label: '2 (Segunda)' },
  { value: '3', label: '3 (Tercera)' },
  { value: '4', label: '4 (Cuarta)' },
  { value: '5', label: '5 (Quinta)' },
  { value: '6', label: '6 (Última)' },
];

// Day of Week (1=Sunday … 7=Saturday)
const dayOfWeekOptions = [
  { value: '1', label: 'Domingo' },
  { value: '2', label: 'Lunes' },
  { value: '3', label: 'Martes' },
  { value: '4', label: 'Miércoles' },
  { value: '5', label: 'Jueves' },
  { value: '6', label: 'Viernes' },
  { value: '7', label: 'Sábado' },
];

const usHolidays = [
  {
    label: "Víspera de Año Nuevo (31 de diciembre)",
    value: { mon: "12",  wday: "",   mday: "31",      mweek: "" }
  },
  {
    label: "Año Nuevo (1 de enero)",
    value: { mon: "1",  wday: "",   mday: "1",      mweek: "" }
  },
  {
    label: "Día de Martin Luther King Jr. (3er lunes de enero)",
    value: { mon: "1",  wday: "2",  mday: "15-21",  mweek: "" }
  },
  {
    label: "Día de San Valentín (14 de febrero)",
    value: { mon: "2",  wday: "",   mday: "14",     mweek: "" }
  },
  {
    label: "Día de los Presidentes (3er lunes de febrero)",
    value: { mon: "2",  wday: "2",  mday: "15-21",  mweek: "" }
  },
  {
    label: "Día de San Patricio (17 de marzo)",
    value: { mon: "3",  wday: "",   mday: "17",     mweek: "" }
  },
  {
    label: "Día de los Caídos (último lunes de mayo)",
    value: { mon: "5",  wday: "2",  mday: "25-31",  mweek: "" }
  },
  {
    label: "Juneteenth (19 de junio)",
    value: { mon: "6",  wday: "",   mday: "19",     mweek: "" }
  },
  {
    label: "Día de la Independencia (4 de julio)",
    value: { mon: "7",  wday: "",   mday: "4",      mweek: "" }
  },
  {
    label: "Día del Trabajo (1er lunes de septiembre)",
    value: { mon: "9",  wday: "2",  mday: "1-7",    mweek: "" }
  },
  {
    label: "Día de la Raza (2do lunes de octubre)",
    value: { mon: "10", wday: "2",  mday: "8-14",   mweek: "" }
  },
  {
    label: "Halloween (31 de octubre)",
    value: { mon: "10", wday: "",   mday: "31",     mweek: "" }
  },
  {
    label: "Día de los Veteranos (11 de noviembre)",
    value: { mon: "11", wday: "",   mday: "11",     mweek: "" }
  },
  {
    label: "Día de Acción de Gracias (4to jueves de noviembre)",
    value: { mon: "11", wday: "5",  mday: "22-28",  mweek: "" }
  },
  {
    label: "Viernes Negro (4to viernes de noviembre)",
    value: { mon: "11", wday: "6",  mday: "23-29",  mweek: "" }
  },
  {
    label: "Nochebuena (24 de diciembre)",
    value: { mon: "12", wday: "",   mday: "24",     mweek: "" }
  },
  {
    label: "Navidad (25 de diciembre)",
    value: { mon: "12", wday: "",   mday: "25",     mweek: "" }
  },
  {
    label: "Día de la Madre (2do domingo de mayo)",
    value: { mon: "5",  wday: "1",  mday: "8-14",   mweek: "" }
  },
  {
    label: "Día del Padre (3er domingo de junio)",
    value: { mon: "6",  wday: "1",  mday: "15-21",  mweek: "" }
  }
];

const caHolidays = [
    {
        label: "Año Nuevo (1 de enero)",
        value: { mon: "1", wday: "", mday: "1", mweek: "" }
    },
    {
        label: "Día de la Familia (3er lunes de febrero)",
        value: { mon: "2", wday: "2", mday: "15-21", mweek: "" }
    },
    {
        label: "Viernes Santo (viernes antes del Domingo de Pascua)",
        value: { mon: "4", wday: "6", mday: "2-8", mweek: "" }
    },
    {
        label: "Lunes de Pascua (lunes después del Domingo de Pascua)",
        value: { mon: "4", wday: "2", mday: "1-7", mweek: "" }
    },
    {
        label: "Día de Victoria (último lunes antes del 25 de mayo)",
        value: { mon: "5", wday: "2", mday: "18-24", mweek: "" }
    },
    {
        label: "Día de Canadá (1 de julio)",
        value: { mon: "7", wday: "", mday: "1", mweek: "" }
    },
    {
        label: "Feriado Cívico (1er lunes de agosto)",
        value: { mon: "8", wday: "2", mday: "1-7", mweek: "" }
    },
    {
        label: "Día del Trabajo (1er lunes de septiembre)",
        value: { mon: "9", wday: "2", mday: "1-7", mweek: "" }
    },
    {
        label: "Día Nacional de la Verdad y la Reconciliación (30 de septiembre)",
        value: { mon: "9", wday: "", mday: "30", mweek: "" }
    },
    {
        label: "Día de Acción de Gracias (2do lunes de octubre)",
        value: { mon: "10", wday: "2", mday: "8-14", mweek: "" }
    },
    {
        label: "Día del Recuerdo (11 de noviembre)",
        value: { mon: "11", wday: "", mday: "11", mweek: "" }
    },
    {
        label: "Navidad (25 de diciembre)",
        value: { mon: "12", wday: "", mday: "25", mweek: "" }
    },
    {
        label: "Boxing Day (26 de diciembre)",
        value: { mon: "12", wday: "", mday: "26", mweek: "" }
    },
    // Additional observances
    {
        label: "Día de San Patricio (17 de marzo)",
        value: { mon: "3", wday: "", mday: "17", mweek: "" }
    },
    {
        label: "Día de la Madre (2do domingo de mayo)",
        value: { mon: "5", wday: "1", mday: "8-14", mweek: "" }
    },
    {
        label: "Día del Padre (3er domingo de junio)",
        value: { mon: "6", wday: "1", mday: "15-21", mweek: "" }
    },
    {
        label: "Halloween (31 de octubre)",
        value: { mon: "10", wday: "", mday: "31", mweek: "" }
    }
];

const ukHolidays = [
    {
        label: "Año Nuevo (1 de enero)",
        value: { mon: "1", wday: "", mday: "1", mweek: "" }
    },
    {
        label: "Día de Mayo (1er lunes de mayo)",
        value: { mon: "5", wday: "2", mday: "1-7", mweek: "" }
    },
    {
        label: "Feriado bancario de primavera (último lunes de mayo)",
        value: { mon: "5", wday: "2", mday: "25-31", mweek: "" }
    },
    {
        label: "Feriado bancario de agosto (último lunes de agosto)",
        value: { mon: "8", wday: "2", mday: "25-31", mweek: "" }
    },
    {
        label: "Feriado bancario de agosto (1er lunes de agosto; solo Escocia)",
        value: { mon: "8", wday: "2", mday: "1-7", mweek: "" }
    },
    {
        label: "Navidad (25 de diciembre)",
        value: { mon: "12", wday: "", mday: "25", mweek: "" }
    },
    {
        label: "Boxing Day (26 de diciembre)",
        value: { mon: "12", wday: "", mday: "26", mweek: "" }
    },
    // Additional observances
    {
        label: "Día de San Patricio (17 de marzo)",
        value: { mon: "3", wday: "", mday: "17", mweek: "" }
    },
    {
        label: "Día de San Andrés (30 de noviembre)",
        value: { mon: "11", wday: "", mday: "30", mweek: "" }
    },
    {
        label: "Día de la Madre (2do domingo de mayo)",
        value: { mon: "5", wday: "1", mday: "8-14", mweek: "" }
    },
    {
        label: "Día del Padre (3er domingo de junio)",
        value: { mon: "6", wday: "1", mday: "15-21", mweek: "" }
    },
    {
        label: "Halloween (31 de octubre)",
        value: { mon: "10", wday: "", mday: "31", mweek: "" }
    }
];

</script>

<style>
div[data-lastpass-icon-root] {
    display: none !important
}

div[data-lastpass-root] {
    display: none !important
}
</style>
