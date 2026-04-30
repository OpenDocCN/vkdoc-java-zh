# 更新歌手
mutation {
updateSinger(id: 16, singer: {
firstName: "Lindsey"
lastName: "Buckingham"
genre: "rock"
birthDate: "1949-10-03"
}) {
id
firstName
lastName
}
}
