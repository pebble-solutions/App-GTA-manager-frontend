<template>
    <div class="card-header text-center" :class="headerClass">
        <i class="bi bi-lock-fill float-start" v-if="!selectable"></i>
        <i class="bi bi-sign-merge-left text-warning" v-if="importedIcon"></i>
        <i :class="headerIcon" v-if="headerIcon"></i>
        <i class="bi bi-chat-fill mx-2" v-if="hasComment"></i>{{headerLabel}}
        
    </div>
</template>

<script>

import {getWorkingTime} from '../../js/std'
import {numberToTimeString} from '../../js/date'

export default {
    props: {
        GtaPeriode: Object,
        selectable: Boolean,
        hasComment: Number,
    },

    computed: {
        /**
         * Retourne le nom de la classe CSS du header en fonction de l'état de validation de la période
         * - success : validée
         * - danger : refusée
         * - light : en attente
         * 
         * @return {string}
         */
        headerClass() {

            let pointer = this.selectable ? "cursor-pointer " : "";

            if (this.GtaPeriode.valider === "OUI") return pointer+"text-bg-success";
            else if (this.GtaPeriode.valider == "NON") return pointer+"text-bg-danger";
            else return pointer+"text-bg-light";
        },

        /**
         * Retourne le temps de travail de la période.
         * Le temps de travail = amplitude - pause.
         * 
         * @return {string} H:MM
         */
        workingTime() {
            let duration = getWorkingTime(this.GtaPeriode.structure_temps_declarations);
            return numberToTimeString(duration);
        },

        /**
         * Retourne un libellé pour le header. Si il s'agit d'une déclaration d'absence,
         * le libellé affiche Absence. Sinon, c'est le temps cumulé de la déclaration qui 
         * est pris.
         * 
         * @return {string}
         */
        headerLabel() {
            return this.GtaPeriode.gta_absence_id ? "Absence" : this.workingTime;
        },

        /**
         * Retourne un icon en fonctio du type de période déclarée ou rien.
         */
        headerIcon() {
            return this.GtaPeriode.gta_absence_id ? "bi bi-brightness-high" : null;
        },

        /**
         * Retourne true si la période est issue d'un import non vérifié
         * 
         * Un import n'est pas vérifié lorsque id_edi a une valeur et que valider est NULL
         * 
         * @return {bool}
         */
        importedIcon() {
            return this.GtaPeriode.id_edi && !this.GtaPeriode.valider;
        }
    }
}

</script>