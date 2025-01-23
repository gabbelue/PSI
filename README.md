from sqlalchemy import create_engine, ForeignKey
from sqlalchemy.orm import Session
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column
from sqlalchemy.orm import Relationship
from sqlalchemy import select
from typing import List


engine = create_engine('sqlite:///exemplo1.db')
session = Session(bind=engine)

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = 'users'
    id: Mapped[int] = mapped_column(primary_key=True)
    nome: Mapped[str]
    gerente_id = mapped_column(
        ForeignKey('users.id'),
        nullable=True)

    gerenciados: Mapped[List['User']]= Relationship('User',
    back_populates= 'gerente')

    gerente = Relationship('User',
    back_populates= 'gerenciados',
    remote_side=[id])

    def __repr__(self) -> str:
        return self.nome

Base.metadata.create_all(bind=engine)

user1 = User(nome = "Monicke", gerente_id=1)
user2 = User(nome = "Eunice", gerente_id=1)
user3 = User(nome = "Lívia", gerente_id=1)
user4 = User(nome = "Luiza", gerente_id=1)
user5 = User(nome = "Anna Júlia", gerente_id=1)
user6 = User(nome = "Wesley", gerente_id=1)

session.add_all([user1, user2, user3, user4, user5, user6])
session.commit()

sttm = select(User).where(User.id == 1)
print(sttm)

chefe = session.execute(sttm).scalars().first()
print(chefe.nome)
print(chefe.gerenciados)

sttm = select(User).where(User.id == 2)
pessoa = session.execute(sttm).scalars().first()
print ("Eu sou:" + str(pessoa))
print("Meu chefe é:" + str(pessoa.gerente))
