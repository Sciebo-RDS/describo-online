<template>
    <div>
        <div v-if="property.value" class="w-full">
            <date-component
                :property="property.name"
                :value.sync="property.value"
                @save:property="savePropertyValue"
                v-if="isDate(property.value)"
            />
            <date-time-component
                :property="property.name"
                :value.sync="property.value"
                @save:property="savePropertyValue"
                v-if="isDateTime(property.value)"
            />
            <time-component
                :property="property.name"
                :value.sync="property.value"
                @save:property="savePropertyValue"
                v-if="isTime(property.value)"
            />
            <number-component
                :property="property.name"
                :value.sync="property.value"
                @save:property="savePropertyValue"
                v-if="isNumber(property.value)"
            />
            <text-component
                v-if="isText(property.value) && !isValue() && !isSelect()"
                :style="inputElementWidth"
                :type="definition.type[0].toLowerCase()"
                :property="property.name"
                :value.sync="property.value"
                :definition="definition"
                @save:property="savePropertyValue"
            />
            <value-component :definition="definition" v-if="isValue()" />
            <select-component
                v-if="isSelect()"
                :style="inputElementWidth"
                :property="property.name"
                :value.sync="property.value"
                :definition="definition"
                @save:property="savePropertyValue"
            />
        </div>
        <div v-else>
            <render-linked-item-component :entity="property" @delete:property="deleteProperty" />
        </div>
    </div>
</template>

<script>
import RenderLinkedItemComponent from "./RenderLinkedItem.component.vue";
import RenderReverseItemLinkComponent from "./RenderReverseItemLink.component.vue";
import TextComponent from "./Text.component.vue";
import DateComponent from "./Date.component.vue";
import DateTimeComponent from "./DateTime.component.vue";
import TimeComponent from "./Time.component.vue";
import NumberComponent from "./Number.component.vue";
import ValueComponent from "./Value.component.vue";
import SelectComponent from "./Select.component.vue";
import { parseISO, startOfDay } from "date-fns";
import { isDate, isDecimal, isInt, isFloat, isNumeric } from "validator";

export default {
    components: {
        RenderLinkedItemComponent,
        RenderReverseItemLinkComponent,
        TextComponent,
        DateComponent,
        DateTimeComponent,
        NumberComponent,
        TimeComponent,
        ValueComponent,
        SelectComponent,
    },
    props: {
        property: {
            type: Object,
            required: true,
        },
        definition: {
            type: Object,
            default: () => ({
                type: "Text",
            }),
        },
    },
    data() {
        return {
            loading: false,
        };
    },
    computed: {
        inputElementWidth() {
            return `width: 500px;`;
        },
    },
    methods: {
        async savePropertyValue(data) {
            this.$emit("save:property", {
                entityId: this.property.entityId,
                propertyId: this.property.id,
                property: data.property,
                value: data.value,
            });
        },
        deleteProperty(data) {
            this.$emit("delete:property", data);
        },
        isDate(string) {
            const date = parseISO(string);
            return (
                isDate(date) &&
                date.toISOString() === startOfDay(date).toISOString() &&
                !this.isNumber(string)
            );
        },
        isDateTime(string) {
            const date = parseISO(string);
            return (
                isDate(date) &&
                !this.isNumber(string) &&
                date.toISOString() !== startOfDay(date).toISOString()
            );
        },
        isTime(string) {
            return string.match(/^([0-9]|0[0-9]|1[0-9]|2[0-3]):[0-5][0-9]$/);
        },
        isText(string) {
            if (
                !this.isDate(string) &&
                !this.isDateTime(string) &&
                !this.isTime(string) &&
                !this.isNumber(string)
            )
                return true;
        },
        isNumber(string) {
            return isDecimal(string) || isInt(string) || isFloat(string) || isNumeric(string);
        },
        isValue() {
            return this.definition?.type === "Value";
        },
        isSelect() {
            return this.definition?.values?.includes(this.property.value);
        },
    },
};
</script>
