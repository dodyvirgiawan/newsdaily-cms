<template>
    <tr>
        <td>{{ index + 1 }}</td>
        <td>{{ historyData.title }}</td>
        <td>{{ historyData.description }}</td>
        <td>{{ historyData.User.email }}</td>
        <td>{{ formattedDate }}</td>
    </tr>
</template>

<script>
export default {
    name: 'HistoryRowData',
    props: ['historyData', 'index'],
    computed: {
        formattedDate() {
            const date = new Date(this.historyData.createdAt)
            return `${date.toLocaleDateString('en-US')}, ${date.toLocaleTimeString('en-US')}`
        }
    }
}
</script>

<style>

</style>