# 创建歌手
mutation {
newSinger(singer: {
firstName: "Lindsey"
lastName: "Buckingham"
pseudonym: "The Greatest"
genre: "rock"
birthDate: "1949-10-03"
}) {
id
firstName
lastName
}
}
